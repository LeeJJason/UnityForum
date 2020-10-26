# Bindings
* * 绑定的目的是将对象内的属性同步到可见的UI。绑定是指属性和对其进行修改的可视控件之间的链接。

在对象和任何从BindableElement派生或实现IBindable接口的UIElement之间完成绑定。

从UnityEditor.UIElements命名空间：
**Base Class:*
* BaseCompositeField
* BasePopupField
* CompoundFields
* TextValueField

**Controls:**
* InspectorElement
* ProgressBar
* BoundsField
* BoundsIntField
* ColorField
* CurveField
* DoubleField
* EnumField
* FloatField
* GradientField
* IntegerField
* LayerField
* LayerMaskField
* LongField
* MaskField
* ObjectField
* PopupField
* PropertyControl
* RectField
* RectIntField
* TagField
* Vector2Field
* Vector2IntField
* Vector3Field
* Vector3IntField
* Vector4Field

从UnityEngine.UIElements命名空间：

**Base Class**
* BaseField
* BaseSlider
* TextInputBaseField
* TemplateContainer

**Controls**
* Foldout
* MinMaxSlider
* Slider
* SliderInt
* TextField
* Toggle

通过使用上面列出的命名空间之一中的控件，同时按照以下步骤进行绑定。

1. 在控件中，从IBindable接口指定bindingPath，以便UI知道要绑定的属性。您可以在C＃或UXML中执行此操作。本主题的后面部分提供了每个示例。
2. 为要绑定的对象创建一个SerializedObject。
3. 将此对象绑定到UIElements控件或其父对象之一。

## Binding with C#
以下代码片段显示了如何使用C＃代码创建绑定。要使用此代码段，请将此示例另存为C＃文件在项目中的编辑器文件夹中。将C＃文件命名为SimpleBindingExample.cs。

SimpleBindingExample.cs的内容：
```cs
using UnityEditor;
using UnityEngine;
using UnityEditor.UIElements;
using UnityEngine.UIElements;

namespace UIElementsExamples
{
   public class SimpleBindingExample : EditorWindow
   {
       TextField m_ObjectNameBinding;
      
       [MenuItem("Window/UIElementsExamples/Simple Binding Example")]
       public static void ShowDefaultWindow()
       {
           var wnd = GetWindow<SimpleBindingExample>();
           wnd.titleContent = new GUIContent("Simple Binding");
       }
    
       public void OnEnable()
       {
           var root = this.rootVisualElement;
           m_ObjectNameBinding = new TextField("Object Name Binding");
           m_ObjectNameBinding.bindingPath = "m_Name";
           root.Add(m_ObjectNameBinding);
           OnSelectionChange();
       }
    
       public void OnSelectionChange()
       {
           GameObject selectedObject = Selection.activeObject as GameObject;
           if (selectedObject != null)
           {
               // Create serialization object
               SerializedObject so = new SerializedObject(selectedObject);
               // Bind it to the root of the hierarchy. It will find the right object to bind to...
               rootVisualElement.Bind(so);
    
               // ... or alternatively you can also bind it to the TextField itself.
               // m_ObjectNameBinding.Bind(so);
           }
           else
           {
               // Unbind the object from the actual visual element
               rootVisualElement.Unbind();
              
               // m_ObjectNameBinding.Unbind();
              
               // Clear the TextField after the binding is removed
               m_ObjectNameBinding.value = "";
           }
       }
   }
}
```

在Unity中，选择**Window > UIElementsExamples > Simple Binding Example**。您可以使用此窗口选择场景中的任何GameObject，并使用显示的TextField修改其名称。

## Binding with UXML
本节说明如何通过UXML层次结构设置使用绑定。

在UXML中，属性绑定路径是在TextField控件中定义的。这就是将控件绑定到对象的有效属性的原因。

SimpleBindingExample.uxml的内容：
```xml
<UXML xmlns:ui="UnityEngine.UIElements">
 <ui:VisualElement name="top-element">
   <ui:Label name="top-label" text="UXML-Defined Simple Binding"/>
   <ui:TextField name="GameObjectName" label="Name" text="" binding-path="m_Name"/>
 </ui:VisualElement>
</UXML>
```

SimpleBindingExample.cs的内容：
```cs
using UnityEditor;
using UnityEngine;
using UnityEditor.UIElements;
using UnityEngine.UIElements;


namespace UIElementsExamples
{
   public class SimpleBindingExampleUXML : EditorWindow
   {
       [MenuItem("Window/UIElementsExamples/Simple Binding Example UXML")]
       public static void ShowDefaultWindow()
       {
           var wnd = GetWindow<SimpleBindingExampleUXML>();
           wnd.titleContent = new GUIContent("Simple Binding UXML");
       }

       public void OnEnable()
       {
           var root = this.rootVisualElement;
           var visualTree = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>("Assets/Editor/SimpleBindingExample.uxml");
           visualTree.CloneTree(root);
           OnSelectionChange();
       }
    
       public void OnSelectionChange()
       {
           GameObject selectedObject = Selection.activeObject as GameObject;
           if (selectedObject != null)
           {
               // Create serialization object
               SerializedObject so = new SerializedObject(selectedObject);
               // Bind it to the root of the hierarchy. It will find the right object to bind to...
               rootVisualElement.Bind(so);
           }
           else
           {
               // Unbind the object from the actual visual element
               rootVisualElement.Unbind();
              
               // Clear the TextField after the binding is removed
               // (this code is not safe if the Q() returns null)
               rootVisualElement.Q<TextField>("GameObjectName").value = "";
           }
       }
   }
}
```

## Using bindings within InspectorElement
InspectorElement是检查器的UIElement对应物这意味着特定类型的Unity对象。使用InspectorElement检查对象具有以下优点：
* 创建UI。
* 自动绑定对象和UI。

在`Assets/Editor/SimpleBindingExample.cs`下找到另一个简单的绑定示例，其中提供了用法示例和过程概述。  
`Assets/Editor/SimpleBindingExample.cs`的内容：
```cs
using UnityEditor;
using UnityEngine;
using UnityEditor.UIElements;

namespace UIElementsExamples
{
   public class SimpleBindingExampleUXML : EditorWindow
   {
       [MenuItem("Window/UIElementsExamples/Simple Binding Example UXML")]
       public static void ShowDefaultWindow()
       {
           var wnd = GetWindow<SimpleBindingExampleUXML>();
           wnd.titleContent = new GUIContent("Simple Binding UXML");
       }

       TankScript m_Tank;
       public void OnEnable()
       {
           m_Tank = GameObject.FindObjectOfType<TankScript>();
           if (m_Tank == null)
               return;
    
           var inspector = new InspectorElement(m_Tank);
           rootVisualElement.Add(inspector);
       }
   }
}
```

此代码引用TankScript脚本并使用InspectorElement。 TankScript脚本是分配给GameObject的MonoBehaviour的示例。

Assets / TankScript.cs的内容：
```cs
using UnityEngine;

[ExecuteInEditMode]
public class TankScript : MonoBehaviour
{
   public string tankName = "Tank";
   public float tankSize = 1;

   private void Update()
   {
       gameObject.name = tankName;
       gameObject.transform.localScale = new Vector3(tankSize, tankSize, tankSize);
   }
}
```

InspectorElement是使用特定的UI定制的。这是通过TankEditor脚本完成的。 TankEditorscript为TankScript类型定义自定义编辑器。 TankEditorscript还使用UXML文件作为层次结构，并使用USS文件设置检查器的样式。

Assets / Editor / TankEditor.cs的内容：
```cs
using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;

[CustomEditor(typeof(TankScript))]
public class TankEditor : Editor
{
   public override VisualElement CreateInspectorGUI()
   {
       var visualTree = Resources.Load("tank_inspector_uxml") as VisualTreeAsset;
       var uxmlVE = visualTree.CloneTree();
uxmlVE.styleSheets.Add(AssetDatabase.LoadAssetAtPath<StyleSheet>("Assets/Resources/tank_inspector_styles.uss"));
      return uxmlVE;
   }
}
```

Assets / Resources / tank_inspector_uxml.uxml的内容：
```xml
<UXML xmlns:ui="UnityEngine.UIElements" xmlns:ue="UnityEditor.UIElements">
   <ui:VisualElement name="row" class="container">
       <ui:Label text="Tank Script - Custom Inspector" />
       <ue:PropertyField binding-path="tankName" name="tank-name-field" />
       <ue:PropertyField binding-path="tankSize" name="tank-size-field" />
   </ui:VisualElement>
</UXML>
```

UXML文件tank_inspector_uxml.uxml指定绑定。
具体来说，对于每个PropertyFields标记，每个绑定路径属性都设置为要绑定的属性。 
UI中显示的元素基于每个绑定属性的类型。

Assets / Resources / tank_inspector_styles.uss的内容：

```
.container {
   background-color: rgb(80, 80, 80);
   flex-direction: column;
}
Label {
   background-color: rgb(80, 80, 80);
}
TextField:hover {
   background-color: rgb(255, 255, 0);
}
FloatField {
   background-color: rgb(0, 0, 255);
}
```

USS文件tank_inspector_styles.uss定义每个元素的样式。

下表列出了PropertyField支持的字段。每个字段都包含其数据类型。
|Field|Type
|:----|:----
|BoundsField|Bounds
|BoundsIntField|BoundsInt
|ColorField|Color
|CurveField|AnimationCurve
|FloatField|float
|GradientField|Gradient
|IntegerField|int
|IntegerField|int, for the ArraySize
|LayerMaskField|unit
|ObjectField|UnityEngine.Object
|PopupFieldstring>|Enum
|RectField|Rect
|RectIntField|RecInt
|TextField|string
|TextField with a maxLength=1|char
|Toggle|bool
|Vector2Field|Vector2
|Vector2IntField|Vector2Int
|Vector3Field|Vector3
|Vector3IntField|Vector3Int
|Vector4Field|Vector4