# Styles and Unity style sheets
每个VisualElement包含样式属性，这些属性设置元素的尺寸以及如何在屏幕上绘制元素，例如`backgroundColor`或`borderColor`。

样式属性可以在C＃中设置，也可以从样式表中设置。样式属性被重新分组到它们自己的数据结构（IStyle接口）中。

UIElements支持以USS编写的样式表（统一样式表）。 USS文件是受HTML层叠样式表（CSS）启发的文本文件。 USS格式类似于CSS，但是USS包含替代和自定义项，以便更好地与Unity配合使用。

本节详细介绍了USS，其语法以及与CSS相比的区别。

## Definition of a Unity style sheet
Unity样式表（USS）的基本构建块如下：
* USS是被视为资产的文本文件。文本文件必须具有`.uss`扩展名。
* USS仅支持样式规则。
* 样式规则由选择器和声明块组成。
* 选择器标识样式规则影响哪个视觉元素。
* 用大括号括起来的声明块包含一个或多个样式声明。每个样式声明都包含一个属性和一个值。每个样式声明均以分号结尾。
* 每个样式属性的值是一个文字，在解析后，该文字必须与目标属性名称匹配。

样式规则的一般语法为：
```
selector {
  property1:value;
  property2:value;
}
```

## Attaching USS to visual elements
您可以将Unity样式表（USS）附加到任何可视元素。样式规则适用于视觉元素及其所有后代。必要时，样式表也会自动重新应用。

使用标准Unity API（例如AssetDatabase.Load（）或Resources.Load（））加载StyleSheet对象。使用VisualElement.styleSheets.Add（）方法将样式表附加到视觉元素。

如果在运行EditorWindow的同时修改USS文件，则会立即应用样式更改。

样式应用程序的过程对于使用UIElements的开发人员是透明的。样式表会在需要时自动重新应用（层次结构更改，样式表重新加载）。

## Style matching with rules
定义样式表后，即可将其应用于视觉元素的UIElements树。

在此过程中，会将选择器与元素进行匹配，以解析从USS文件应用哪些属性。如果选择器与元素匹配，则样式声明将应用于该元素。

例如，以下规则与任何Button对象匹配：
```
Button {
  width: 200px;
}
```

## VisualElement matching
UIElement使用以下条件将视觉元素与其样式规则进行匹配：
* 它的C＃类名称（总是最派生的类）
* 一个字符串的名称属性
* 用一组字符串表示的类列表
* VisualElement在视觉树中的祖先和位置

这些特征可以在样式表的选择器中使用。

如果您熟悉CSS，则可以看到与HTML标记名称，id属性和class属性的相似性。