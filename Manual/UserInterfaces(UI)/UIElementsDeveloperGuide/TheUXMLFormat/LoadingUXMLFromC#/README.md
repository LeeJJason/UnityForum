# Loading UXML from C#
要从UXML模板构建用户界面，必须首先将模板加载到VisualTreeAsset中：
```cs
var template = EditorGUIUtility.Load("path/to/file.uxml") as VisualTreeAsset;
```
或更直接地：
```cs
var template = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>("path/to/file.uxml");
```
然后，您可以构建表示的可视树并将其附加到父元素：
```cs
template.CloneTree(parentElement, slots);
```

在上面的声明中，模板中的`<UXML>`元素未转换为VisualElement。而是将其所有子级附加到parentElement指定的元素上。

实例化模板后，您可以使用UQuery：Unity的JQuery / Linq实现从可视元素树中检索特定元素。

例如，以下代码演示如何创建新的EditorWindow并加载UXML文件作为其内容：
```cs
public class MyWindow : EditorWindow  {
    [MenuItem ("Window/My Window")]
    public static void  ShowWindow () {
        EditorWindow w = EditorWindow.GetWindow(typeof(MyWindow));

        VisualTreeAsset uiAsset = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>("Assets/MyWindow.uxml");
        VisualElement ui = uiAsset.CloneTree(null);

        w.rootVisualElement.Add(ui);
    }

    void OnGUI () {
        // Nothing to do here, unless you need to also handle IMGUI stuff.
    }
}
```