# The UXML format
UXML文件是定义用户界面的逻辑结构的文本文件。 UXML文件中使用的格式受HTML（超文本标记语言），XAML（可扩展应用程序标记语言）和XML（可扩展标记语言）的启发。如果您熟悉这些公认的格式，则应该注意到UXML中的许多相似之处。但是，UXML格式包含一些细微差别，以提供使用Unity的有效方法。

本节描述了Unity支持的UXML格式，并提供了有关编写，加载和定义UXML模板的详细信息。它还包括有关定义新元素以及如何使用UQuery的信息。

UXML使技术含量较低的用户可以更轻松地在Unity中构建用户界面。在UXML中，您可以：
* 在XML中定义用户界面（UI）的结构。
* 定义使用USS样式表进行布局UI 。

这使开发人员可以执行技术任务，例如导入资产，定义逻辑和处理数据。

## Defining new elements
UIElements是可扩展的。您可以定义自己的用户界面组件和元素。

在使用UXML文件定义新元素之前，必须从VisualElement或其子类之一派生一个新类，然后在此新类中实现适当的功能。您的新类必须实现默认的构造函数。

例如，以下代码派生新的StatusBar类并实现其默认构造函数：
```cs
class StatusBar : VisualElement
{
    public StatusBar()
    {
        m_Status = String.Empty;
    }

    string m_Status;
    public string status { get; set; }
}
```

为了使UIElement在读取UXML文件时实例化新类，必须为类定义一个工厂。除非工厂需要做一些特殊的事情，否则可以从UxmlFactoy <T>派生工厂。建议您将工厂类放入组件类中。
```cs
class StatusBar : VisualElement
{
    public new class UxmlFactory : UxmlFactory<StatusBar> {}

    // ...
}
```

定义此工厂后，可以在XML文件中使用`<StatusBar>`元素。

## Defining attributes on elements
您可以为新类定义UXML特征，并将其工厂设置为使用这些特征。

例如，以下代码演示如何定义UXML traits类以将status属性初始化为StatusBar类的属性。 status属性是从XML数据初始化的。
```cs
class StatusBar : VisualElement
{
    public new class UxmlFactory : UxmlFactory<StatusBar, UxmlTraits> {}

    public new class UxmlTraits : VisualElement.UxmlTraits
    {
        UxmlStringAttributeDescription m_Status = new UxmlStringAttributeDescription { name = "status" };
        
        public override IEnumerable<UxmlChildElementDescription> uxmlChildElementsDescription
        {
            get { yield break; }
        }

        public override void Init(VisualElement ve, IUxmlAttributes bag, CreationContext cc)
        {
            base.Init(ve, bag, cc);
            ((StatusBar)ve).status = m_Status.GetValueFromBag(bag, cc);
        }
    }

    // ...
}
```
`UxmlTraits`有两个用途：
* 工厂使用它来初始化新创建的对象。
* 模式生成过程对其进行分析，以获取有关元素的信息。此信息被转换为XML模式指令。

上面的代码示例执行以下操作：
* `m_Status`的声明定义了一个名为`status`的XML属性。
* `uxmlChildElementsDescription`返回一个空的`IEnumerable`，它指示`StatusBar`元素没有子级。
* `Init（）`成员从XML解析器读取属性包中的`status`属性值，并将`StatusBar.status`属性设置为此值。
* 通过将`UxmlTraits`类放入`StatusBar`类中，`Init（）`方法可以访问`StatusBar`的私有成员。
* 新的`UxmlTraits`类继承自基类`UxmlTraits`，因此它共享基类的属性。
* `Init（）`调用`base.Init（）`初始化基类属性。

上面的代码示例使用`UxmlStringAttributeDescription`类声明一个字符串属性。 UIElements支持以下类型的属性，每个属性都将C＃类型链接到XML类型：
|Attribute|Attribute value
|:--------|:------------
|UxmlStringAttributeDescription|A string
|UxmlFloatAttributeDescription|A single precision floating point value in the range of the C# float type.
|UxmlDoubleAttributeDescription|A double precision floating point value in the range of the C# double type.
|UxmlIntAttributeDescription|An integer value in the range of the C# int type.
|UxmlLongAttributeDescription|A long integer value in the range of the C# long type.
|UxmlBoolAttributeDescription|true or false 
|UxmlColorAttributeDescription|A string representing a color (#FFFFFF)
|UxmlEnumAttributeDescription<T>|A string representing one of the values for the Enum type T.

在上面的代码示例中，`uxmlChildElementsDescription`返回一个空的IEnumerable，它指示StatusBar元素不接受子级。

要使元素接受任何类型的子代，必须重写`uxmlChildElementsDescription`属性。例如，要使StatusBar元素接受任何类型的子代，必须按如下所示指定`uxmlChildElementsDescription`属性：
```cs
public override IEnumerable<UxmlChildElementDescription> uxmlChildElementsDescription
{
    get
    {
        yield return new UxmlChildElementDescription(typeof(VisualElement));
    }
}
```

## Defining a namespace prefix
在C＃中定义新元素后，就可以开始在UXML文件中使用该元素。如果在新的名称空间中定义了新元素，则应为名称空间定义一个前缀。命名空间前缀被声明为根<UXML>元素的属性，并在对元素进行范围界定时替换完整的命名空间名称。

要定义名称空间前缀，请为您要定义的每个名称空间前缀的程序集添加UxmlNamespacePrefix属性。
```cs
[assembly: UxmlNamespacePrefix("My.First.Namespace", "first")]
[assembly: UxmlNamespacePrefix("My.Second.Namespace", "second")]
```

可以在程序集的任何C＃文件的根级别（任何名称空间之外）进行此操作。

模式生成系统执行以下操作：
* 检查这些属性并使用它们生成架构。
* 将名称空间前缀定义添加为新创建的UXML文件中<UXML>元素的属性
* 在其xsi：schemaLocation属性中包含名称空间的架构文件位置。

您应该更新项目的UXML模式。选择**Assets > Update UIElements Schema**，以确保您的文本编辑器可以识别新元素。

通过在**Project / Assets / Editor**文件夹中选择**Create > UIElements Editor Window**，可以在新创建的UXML中使用定义的前缀。

## Advanced usage
### Customizing a UXML name
您可以通过覆盖UXML名称的IUxmlFactory.uxmlName和IUXmlFactory.uxmlQualifiedName属性来自定义UXML名称。确保uxmlName在您的名称空间内是唯一的，并且uxmlQualifiedName在您的项目中是唯一的。

如果两个名称都不唯一，则在尝试加载程序集时会引发异常。  
以下代码示例演示如何覆盖和定制UXML名称：
```cs
public class FactoryWithCustomName : UxmlFactory<..., ...>
{
    public override string uxmlName
    {
        get { return "UniqueName"; }
    }

    public override string uxmlQualifiedName
    {
        get { return uxmlNamespace + "." + uxmlName; }
    }
}
```

### Selecting a factory for an element
默认情况下，`IUxmlFactory`实例化一个元素并使用该元素的名称选择该元素。

您可以通过覆盖`IUXmlFactory.AcceptsAttributeBag`使选择过程考虑元素上的属性值。然后，工厂将检查元素属性以确定是否可以为UXML元素实例化对象。

例如，如果您的`VisualElement`类是通用的，则这很有用。在这种情况下，专门用于类的类工厂可以检查XML类型属性的值。根据值，实例化可以被接受或拒绝。有关示例，请参见`PropertyControl <T>`的实现。

如果一个以上的工厂可以实例化一个元素，则选择第一个注册的工厂。

### Overriding the default value of a base class attribute
您可以通过在派生的`UxmlTraits`类中设置其`defaultValue`来更改在基类中声明的属性的默认值。

例如，以下代码显示如何更改`m_TabIndex`的默认值：
```cs
class MyElementTraits : VisualElement.UxmlTraits
    {
        public MyElementTraits()
        {
            m_TabIndex.defaultValue = 0;
        }
    }
```

### Accepting any attribute
默认情况下，生成的XML模式指出元素可以具有任何属性。

除`UxmlTraits`类中声明的属性值外，其他属性值均不受限制。这与XML验证器相反，XML验证器检查已声明属性的值是否与其声明相匹配。

其他属性包含在`IUxmlAttributes`包中，该包传递给`IUxmlFactory.AcceptsAttributBag（）`和`IUxmlFactory.Init（）`函数。是否使用这些附加属性取决于工厂的实现。默认行为是放弃附加属性。

这意味着这些附加属性未附加到实例化的VisualElement上，并且无法使用UQuery查询这些属性。

定义新元素时，可以通过在UxmlTraits构造函数中将UxmlTraits.canHaveAnyAttribute属性设置为false来将接受的属性限制为明确声明的属性。

## Using Schema definitions
模式定义文件指定属性以及每个UXML元素可以包含哪些子元素。使用架构定义文件作为编写正确文档和验证文档的指南。

在UXML模板文件中，<UXML>根元素的xsi：noNamespaceSchemaLocation和xsi：schemaLocation属性指定模式定义文件的位置。

选择`Assets > Create > UIElements Editor Window`，以使用项目所使用的VisualElement子类中的最新信息自动更新架构定义。要强制更新UXML架构文件，请选择`Assets > Update UIElements Schema`。

注意：某些文本编辑器无法识别xsi：noNamespaceSchemaLocation属性。如果您的文本编辑器找不到架构定义文件，则还应该指定xsi：schemaLocation属性。