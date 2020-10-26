# The Layout Engine
UIElements包含一个布局引擎，该引擎根据布局和样式属性定位视觉元素。布局引擎是[Yoga开源项目](https://github.com/facebook/yoga)，该项目实现了Flexbox的子集：HTML / CSS布局系统。要开始使用Yoga和Flexbox，请查阅以下外部资源：
* [Yoga official documentation](https://yogalayout.com/): the mapping of properties is almost 1-to–1
* [CSS-Tricks guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/): most properties are supported with some minor differences

默认情况下，所有视觉元素都是布局的一部分。该布局具有以下默认行为：
* 容器垂直分布其子级。
* 容器矩形的位置包括其子矩形。此行为可能会受到其他布局属性的限制。
* 带有文本的视觉元素在其大小计算中使用文本大小。此行为可能会受到其他布局属性的限制。

UIElement包含用于标准UI的[内置控件](https://docs.unity3d.com/Manual/UIE-Controls.html)，例如按钮，切换，文本字段或标签。这些内置控件的样式会影响其布局。

以下列表提供了有关如何使用布局引擎的提示：
* 设置`width`和`height`以定义元素的大小。
* 使用`flexGrow`属性（在USS中：`flex-grow：<value>;`）为元素分配灵活大小。当元素的大小由其同级元素确定时，`flexGrow`属性的值用作权重。
* 将`flexDirection`属性设置为`row`（在USS：`flex-direction：row;`中）以切换到水平布局。
* 使用相对定位可根据元素的原始布局位置使其偏移。
* 将`position`属性设置为`absolute`，以相对于其父位置矩形放置一个元素。在这种情况下，它不会影响其兄弟姐妹或父母的布局。