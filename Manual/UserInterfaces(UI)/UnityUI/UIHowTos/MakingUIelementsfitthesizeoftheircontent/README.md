# [Making UI elements fit the size of their content](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/HOWTO-UIFitContentSize.html)
通常，当使用Rect变换放置UI元素时，将手动指定其位置和大小（可选地包括使用父Rect变换进行拉伸的行为）。

但是，有时您可能希望自动调整矩形的大小以适合UI元素的内容。 这可以通过添加一个称为**Content Size Fitter**的组件来完成。

## Fit to size of Text
为了使带有文本组件的Rect变换适合文本内容，请将**Content Size Fitter**组件添加到具有**Text**组件的同一游戏对象中。 然后将**Horizontal Fit**和**Vertical Fit**下拉列表均设置为**Preferred**设置。

### How does it work?
这里发生的是，文本组件用作布局元素，可以提供有关其最小和首选大小的大小的信息。 在手动布局中，不使用此信息。 **Content Size Fitter**是一种布局控制器，它侦听布局元素提供的布局信息，并据此控制Rect变换的大小。

### Remember the pivot
当UI元素自动调整大小以适合其内容时，您应该格外注意Rect Transform的枢轴。 调整元素大小时，枢轴将保持在原位，因此通过设置枢轴位置，您可以控制元素沿哪个方向扩展或收缩。 例如，如果枢轴在中心，则元素将在所有方向均等地扩展；如果枢轴在左上角，则元素将向右和向下扩展。

## Fit to size of UI element with child Text
如果您有一个UI元素（例如Button），它具有背景图像和一个带有Text组件的子游戏对象，则您可能希望整个UI元素适合文本的大小-可能带有一些填充。

为此，首先将一个**Horizontal Layout Group**添加到UI元素，然后再添加一个**Content Size Fitter**。 将Horizontal Fit，Vertical Fit或两者都设置为首选设置。 您可以使用**Horizontal Layout Group**组中的padding属性添加和调整padding。

为什么要使用Horizontal Layout Group？ 嗯，也可能是Vertical Layout Group-只要只有一个孩子，他们就会产生相同的结果。

### How does it work?
水平（或垂直）布局组既可以用作布局控制器，又可以用作布局元素。首先，它侦听组中的子级提供的布局信息-在本例中为子Text。然后，它确定组必须（至少，最好是）有多大才能包含所有子项，并且它充当布局元素，提供有关其最小和首选大小的信息。

**Content Size Fitter**侦听同一游戏对象上任何布局元素所提供的布局信息-在这种情况下，该信息由水平（或垂直）布局组提供。然后根据其设置，根据此信息控制Rect转换的大小。

设置Rect变换的大小后，“水平（或垂直）”布局组将确保根据可用空间定位其子级并为其设置尺寸。有关如何控制子级的位置和大小的更多信息，请参见有关“水平布局组”的页面。

## Make children of a Layout Group fit their respective sizes
如果您有一个布局组（水平或垂直），并且希望该组中的每个UI元素都适合其各自的内容，您该怎么办？

您不能在每个孩子身上放置内容大小适合者。原因是Content Size Fitter希望控制自己的Rect转换，但是父布局组也希望控制子Rect转换。这会产生冲突，结果是未定义的行为。

但是，也没有必要。父级布局组已经可以使每个子级适应内容的大小。您需要做的是在布局组上禁用**Child Force Expand**切换。如果子级本身也是布局组，则可能还需要禁用这些子级上的**Child Force Expand**切换。

一旦子级不再以灵活的宽度扩展，则可以使用**Child Alignment**设置在布局组中指定其对齐方式。

如果您希望某些孩子扩大而不是其他孩子扩大可用空间，该怎么办？通过将布局元素组件添加到要扩展的子项并在这些布局元素上启用“灵活宽度”或“灵活高度”属性，可以轻松地控制它。父级布局组仍应禁用**Child Force Expand**切换，否则所有子级将灵活扩展。

### How does it work?
游戏对象可以具有多个组件，每个组件都提供有关最小，首选和灵活尺寸的布局信息。 优先级系统确定哪些值优先于其他值。 “布局元素”组件的优先级高于“文本”，“图像”和“布局组”组件，因此可用于覆盖它们提供的任何布局信息值。

当布局组收听子级提供的布局信息时，它将考虑被覆盖的灵活尺寸。 然后，在控制孩子的大小时，它不会使孩子的大小超过其首选的大小。 但是，如果布局组启用了**Child Force Expand**选项，则它将始终使所有子级的灵活尺寸至少为1。

## More information
该页面介绍了一些常见用例的解决方案。 有关自动布局系统的更深入的说明，请参见[UI自动布局](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UIAutoLayout.html)页面。