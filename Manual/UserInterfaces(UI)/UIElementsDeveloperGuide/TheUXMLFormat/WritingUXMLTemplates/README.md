# Writing UXML Templates
UXML模板是使用XML标记编写的文本文件，用于定义用户界面的逻辑结构。以下代码示例显示了如何定义一个简单的面板来提示用户进行选择：
```xml
<?xml version="1.0" encoding="utf-8"?>
<UXML
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="UnityEngine.UIElements"
    xsi:noNamespaceSchemaLocation="../UIElementsSchema/UIElements.xsd"
    xsi:schemaLocation="UnityEngine.UIElements ../UIElementsSchema/UnityEngine.UIElements.xsd">

    <Label text="Select something to remove from your suitcase:"/>
    <Box>
        <Toggle name="boots" label="Boots" value="false" />
        <Toggle name="helmet" label="Helmet" value="false" />
        <Toggle name="cloak" label="Cloak of invisibility" value="false"/>
    </Box>
    <Box>
        <Button name="cancel" text="Cancel" />
        <Button name="ok" text="OK" />
    </Box>
</UXML>
```

文件的第一行是XML声明。该声明是可选的。如果包含声明，则声明必须在第一行，并且之前不能出现其他内容或空白。版本属性是必需的。 encoding属性是可选的。如果包括编码，则必须声明文件的字符编码。

下一行定义文档根<UXML>。 <UXML>元素包括名称空间前缀定义和架构定义文件的位置的属性。您可以不按顺序指定这些属性。

在UIElements中，每个元素都在`UnityEngine.UIElements`或`UnityEditor.UIElements`命名空间中定义：
* `UnityEngine.UIElements`命名空间包含定义为Unity Runtime一部分的元素。
* `UnityEditor.UIElements`命名空间包含Unity编辑器中可用的元素。要完全指定一个元素，必须在其名称空间前添加一个元素。

例如，如果要在UXML模板中使用Button元素，则必须指定`<UnityEngine.UIElements：Button />`。

为了使指定名称空间更加容易，您可以定义名称空间前缀。例如，`xmlns：engine =“ UnityEngine.UIElements”`将`engine`前缀定义为`UnityEngine.UIElements`。定义名称空间前缀后，您可以使用它来指定名称空间。例如，`<engine：Button />`等效于`<UnityEngine.UIElements：Button />`

您还可以通过排除前缀来定义默认名称空间。例如，行`xmlns =“ UnityEngine.UIElements”`将`UnityEngine.UIElements`定义为默认名称空间。这意味着指定例如`<Button />`等效于`<UnityEngine.UIElements：Button />`

如果定义自己的元素，则这些元素可能是在其自己的名称空间中定义的。如果要在UXML模板中使用这些元素，则必须在<UXML>标记中包括名称空间定义和模式文件位置，以及Unity名称空间。

通过选择Asset创建新的UXML模板`Asset > Create > UIElements Editor Window`，编辑器会自动为您定义名称空间。

UI定义在<UXML>根节点中。 UI定义是一系列嵌套的XML元素，每个元素代表一个`VisualElement`。

元素名称对应于要实例化的元素的C＃类名称。大多数元素具有属性，并且它们的值映射到C＃中的相应类属性。每个元素都继承其父类类型的属性，可以向其添加自己的属性集。 VisualElement是所有元素的基类，它为所有元素提供以下属性：

* name: 元素的标识符。名称应唯一。
* picking-mode: 设置为`Position`以响应鼠标事件，或者设置为`Ignore`以忽略鼠标事件。
* focus-index: (**OBSOLETE**) Use `tabIndex` and `focusable`.
* tabindex: 一个整数，它定义当前元素的制表符位置。
* focusable: 指示元素是否可聚焦的布尔值。
* class: 用空格分隔的表示元素特征的标识符列表。使用类为元素分配视觉样式。您还可以使用类在UQuery中选择一组元素。
* tooltip: 鼠标悬停在元素上时显示为工具提示的字符串。
* view-data-key : 一个字符串，它定义用于元素序列化的键。

UXML模板示例未定义用户界面的外观。建议您定义样式信息，例如用于绘制UI的尺寸，字体和颜色，在单独的USS文件中（请参见[样式和Unity样式表](https://docs.unity3d.com/Manual/UIE-USS.html)）。

## Adding styles to UXML
要引用样式表文件，UXML文件可以在任何元素声明下使用`<Style>`元素。

例如，如果您在同一文件夹中有一个UXML文件和一个名为“ styles.uss”的USS文件：
```xml
<engine:UXML ...>
    <engine:VisualElement class="root">
        <Style src="styles.uss" />
    </engine:VisualElement>
</engine:UXML>
```
```css
#root {
    width: 200px;
    height: 200px;
    background-color: red;
}
```
注意：Unity不支持根`<UXML>`元素下的`<Style>`元素。

您还可以直接将内联样式声明为UXML元素的属性：
```xml
<engine:UXML ...>
    <engine:VisualElement style="width: 200px; height: 200px; background-color: red;" />
</engine:UXML>
```

## Reusing UXML files
您可以通过简单地在UXML文件中定义组件来创建组件，然后使用另一个UXML文件中的`<Template>`和`<Instance>`元素将其导入。

设计大型用户界面时，您可以创建定义UI部分的模板UXML文件。

您可以在许多地方使用相同的UI定义。例如，假设您有一个肖像UI元素，其中包含图像，名称和标签。您可以创建一个UXML模板文件以在其他UXML文件中重新使用人像UI元素。

例如，假设您在`Assets / Portrait.uxml`文件中有一个Portrait组件：
```xml
<engine:UXML ...>
    <engine:VisualElement class="portrait">
        <engine:Image name="portaitImage" style="--unity-image: url(\"a.png\")"/>
        <engine:Label name="nameLabel" text="Name"/>
        <engine:Label name="levelLabel" text="42"/>
    </engine:VisualElement>
</engine:UXML>
```

您可以将Portrait组件嵌入到其他UXML模板中，如下所示：
```xml
<engine:UXML ...>
    <engine:Template src="/Assets/Portrait.uxml" name="Portrait"/>
    <engine:VisualElement name="players">
        <engine:Instance template="Portrait" name="player1"/>
        <engine:Instance template="Portrait" name="player2"/>
    </engine:VisualElement>
</engine:UXML>
```

## Referencing other files from UXML
UXML文件可以通过元素引用其他UXML文件和USS文件。

`<Template>`和`<Style>`元素都接受“ src”属性或“ path”属性。
* `src`属性允许相对路径，在导入时（例如，文件丢失时）提供错误消息，并确保Unity在播放器版本中正确包含UXML文件引用的资产。
* `path`属性允许使用Unity资源机制，但在导入时不提供错误报告，并且不允许相对路径。

### The `src` attribute
`src`属性期望文件路径相对于项目根目录或包含UXML文件的文件夹。您必须包括文件扩展名。
在以下示例中，UXML文件位于`Assets \ Editor \ UXML`，而USS文件位于`Assets \ Editor \ USS`。
* 根据UXML文件的位置，使用以下示例路径之一：`src="../USS/styles.uss"`或`src="template.uxml"`
* 根据项目的位置，使用以下示例之一作为绝对路径：`src="/Assets/Editor/USS/styles.uss"`或`src="project:/Assets/Editor/UXML/template.uxml"`。

### The `path` attribute
`path`属性接受位于*Resources*文件夹或*Editor Default Resources*文件夹中的文件，并遵循以下规则：
* 如果该文件位于*Resources*文件夹中，则不包括文件扩展名。例如，为位于*Assets/Resources/template.uxml*中的文件编写`path="template"`。
* 如果该文件位于*Editor Default Resources*文件夹中，则必须包括文件扩展名。例如，为位于*Assets/Editor Default Resources/template.uxml*中的文件编写`path="template.uxml"`。