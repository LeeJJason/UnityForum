# [Grid Layout Group](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-GridLayoutGroup.html)
网格布局组组件将其子布局元素放置在网格中。

![](UI_GridLayoutGroupInspector.png)

## Properties
|Property:|Function:
|:--------|:-------
|Padding|布局组边缘内的填充。
|Cell Size|组中每个布局元素要使用的大小。
|Spacing|布局元素之间的间距。
|Start Corner|第一个元素所在的角。
|Start Axis|沿着哪个主轴放置元素。 在开始新行之前，水平将填满整个行。 在开始新列之前，Vertical将填充整个列。
|Child Alignment|如果布局元素未填满所有可用空间，则用于这些元素的对齐方式。
|Constraint|将网格限制为固定数量的行或列，以辅助自动布局系统。

## Description
与其他布局组不同，“网格布局组”将忽略其所包含布局元素的最小，首选和灵活大小属性，而是为所有这些元素分配固定大小，这些大小由“网格布局组”自身的“单元格大小”属性定义。

### Grid Layout Group and auto layout
将网格布局组用作自动布局设置的一部分时，需要注意一些特殊的注意事项，例如将其与[Content Size Fitter](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-ContentSizeFitter.html)一起使用。

自动布局系统独立计算水平和垂直尺寸。 这可能与“网格布局组”不同，在“网格布局组”中，行数取决于列数，反之亦然。

对于任何给定数量的单元格，行计数和列计数都有不同的组合，可以使网格适合其内容。 为了辅助布局系统，可以使用Constraint属性指定要使表具有固定数量的列或行。

以下是将布局系统与内容大小限制器一起使用的建议方法：

#### Flexible width and fixed height
要设置具有灵活宽度和固定高度的网格，该网格在添加更多元素时会水平扩展，您可以如下设置这些属性：
* Grid Layout Group Constraint: Fixed Row Count
* Content Size Fitter Horizontal Fit: Preferred Size
* Content Size Fitter Vertical Fit: Preferred Size or Unconstrained

如果使用不受约束的垂直拟合，则由您决定网格的高度足以适合指定的单元格行数。

#### Fixed width and flexible height
要设置具有固定宽度和灵活高度的网格，该网格在添加更多元素时会垂直扩展，您可以如下设置这些属性：
* Grid Layout Group Constraint: Fixed Column Count
* Content Size Fitter Horizontal Fit: Preferred Size or Unconstrained
* Content Size Fitter Vertical Fit: Preferred Size

如果使用无约束的水平拟合，则由您决定网格的宽度是否足以适合指定的单元格列数。

#### Both flexible width and height
如果您想要一个既具有宽度又具有高度的网格，则可以这样做，但是您将无法控制特定的行数和列数。 网格将尝试使行数和列数大致相同。 您可以如下设置这些属性：
* Grid Layout Group Constraint: Flexible
* Content Size Fitter Horizontal Fit: Preferred Size
* Content Size Fitter Vertical Fit: Preferred Size