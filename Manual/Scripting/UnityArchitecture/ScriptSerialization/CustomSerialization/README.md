# Custom serialization
序列化是将数据结构或对象状态转换为Unity以后可以存储和重构的格式的自动过程。 （有关Unity序列化的更多信息，请参阅[脚本序列化](https://docs.unity3d.com/Manual/script-Serialization.html)的文档。）

有时您可能想要序列化Unity的序列化器不支持的内容。在许多情况下，最好的方法是使用序列化回调。 （有关使用序列化回调的自定义序列化的更多信息，请参见Unity的Scripting API参考：[ISerializationCallbackReceiver](https://docs.unity3d.com/ScriptReference/ISerializationCallbackReceiver.html)。）

序列化回调使您可以在序列化程序从您的字段读取数据之前以及完成对它们的写入之后收到通知。您可以使用序列化回调为难以序列化的数据提供与实际序列化时不同的表示形式。

为此，请将数据转换为Unity想要序列化之前Unity可以理解的内容。然后，在Unity将数据写入字段后，就可以将序列化的表单转换回要在运行时放入数据的表单

例如：您想要一个树数据结构。如果让Unity直接序列化数据结构，则“不支持null”限制将导致您的数据流变得非常大，从而导致许多系统的性能下降。如下面的示例1所示。

**Example 1**: Unity’s direct serlialization, leading to performance issues
```cs
using UnityEngine;
using System.Collections.Generic;
using System;

public class VerySlowBehaviourDoNotDoThis : MonoBehaviour {
    [Serializable]
    public class Node {
        public string interestingValue = "value";
        //The field below is what makes the serialization data become huge because
        //it introduces a 'class cycle'.
        public List<Node> children = new List<Node>();
    }
    //this gets serialized
    public Node root = new Node();
    void OnGUI() {
        Display (root);
    }
    void Display(Node node) {
        GUILayout.Label ("Value: ");
        node.interestingValue = GUILayout.TextField(node.interestingValue, GUILayout.Width(200));
        GUILayout.BeginHorizontal ();
        GUILayout.Space (20);
        GUILayout.BeginVertical ();
        foreach (var child in node.children)
        Display (child);
        if (GUILayout.Button ("Add child"))
        node.children.Add (new Node ());
        GUILayout.EndVertical ();
        GUILayout.EndHorizontal ();
    }
}
```
相反，您告诉Unity不要直接序列化该树，并创建一个单独的字段以适合Unity的序列化器的序列化格式存储树。如下例2所示。

**Example 2**: Avoiding Unity’s direct serlialization and avoiding performance issues
```cs
using System.Collections.Generic;
using System;

public class BehaviourWithTree : MonoBehaviour, ISerializationCallbackReceiver {
    // Node class that is used at runtime.
    // This is internal to the BehaviourWithTree class and is not serialized.
    public class Node {
        public string interestingValue = "value";
        public List<Node> children = new List<Node>();
    }
    // Node class that we will use for serialization.
    [Serializable]
    public struct SerializableNode {
        public string interestingValue;
        public int childCount;
        public int indexOfFirstChild;
    }
    // The root node used for runtime tree representation. Not serialized.
    Node root = new Node();
    // This is the field we give Unity to serialize.
    public List<SerializableNode> serializedNodes;
    public void OnBeforeSerialize() {
        // Unity is about to read the serializedNodes field's contents.
        // The correct data must now be written into that field "just in time".
        if (serializedNodes == null) serializedNodes = new List<SerializableNode>();
        if (root == null) root = new Node ();
        serializedNodes.Clear();
        AddNodeToSerializedNodes(root);
        // Now Unity is free to serialize this field, and we should get back the expected 
        // data when it is deserialized later.
    }
    void AddNodeToSerializedNodes(Node n) {
        var serializedNode = new SerializableNode () {
            interestingValue = n.interestingValue,
            childCount = n.children.Count,
            indexOfFirstChild = serializedNodes.Count+1
        }
        ;
        serializedNodes.Add (serializedNode);
        foreach (var child in n.children)
        AddNodeToSerializedNodes (child);
    }
    public void OnAfterDeserialize() {
        //Unity has just written new data into the serializedNodes field.
        //let's populate our actual runtime data with those new values.
        if (serializedNodes.Count > 0) {
            ReadNodeFromSerializedNodes (0, out root);
        } else
        root = new Node ();
    }
    int ReadNodeFromSerializedNodes(int index, out Node node) {
        var serializedNode = serializedNodes [index];
        // Transfer the deserialized data into the internal Node class
        Node newNode = new Node() {
            interestingValue = serializedNode.interestingValue,
            children = new List<Node> ()
        }
        ;
        // The tree needs to be read in depth-first, since that's how we wrote it out.
        for (int i = 0; i != serializedNode.childCount; i++) {
            Node childNode;
            index = ReadNodeFromSerializedNodes (++index, out childNode);
            newNode.children.Add (childNode);
        }
        node = newNode;
        return index;
    }
    // This OnGUI draws out the node tree in the Game View, with buttons to add new nodes as children.
    void OnGUI() {
        if (root != null)
        Display (root);
    }
    void Display(Node node) {
        GUILayout.Label ("Value: ");
        // Allow modification of the node's "interesting value".
        node.interestingValue = GUILayout.TextField(node.interestingValue, GUILayout.Width(200));
        GUILayout.BeginHorizontal ();
        GUILayout.Space (20);
        GUILayout.BeginVertical ();
        foreach (var child in node.children)
        Display (child);
        if (GUILayout.Button ("Add child"))
        node.children.Add (new Node ());
        GUILayout.EndVertical ();
        GUILayout.EndHorizontal ();
    }
}
```