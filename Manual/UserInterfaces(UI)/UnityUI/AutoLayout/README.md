# Auto Layout
Rect Transform布局系统足够灵活，可以处理许多不同类型的布局，并且还允许以完全自由的形式放置元素。但是，有时可能需要一些结构化的东西。

自动布局系统提供了将元素放置在嵌套**Layout Group**中的方法，例如水平组，垂直组或网格。它还允许根据包含的内容自动调整元素的大小。例如，可以动态调整按钮的大小以完全适合其文本内容以及一些填充。

自动布局系统是在基本Rect Transform布局系统之上构建的系统。可以选择将其用于某些或所有元素。

## Understanding Layout Elements
自动布局系统基于**Layout Element**和**Layout Controller**的概念。**Layout Element**是具有Rect Transform和可选其他组件的游戏对象。**Layout Element**对应具有的大小有一定的了解。**Layout Element**不会直接设置自己的大小，但是充当**Layout Controller**的其他组件可以使用它们提供的信息来计算要用于它们的大小。

**Layout Element**具有定义自己的属性：
* Minimum width
* Minimum height
* Preferred width
* Preferred height
* Flexible width
* Flexible height

使用**Layout Element**提供的信息的**Layout Controller**组件的示例包括**Content Size Fitter**和各种**Layout Group**组件。**Layout Group**中**Layout Element**的大小确定的基本原则如下：
* 分配第一最小尺寸。
* 如果有足够的可用空间，则会分配首选大小。
* 如果有其他可用空间，则会分配灵活的大小。

任何带有Rect Transform的游戏对象都可以用作**Layout Element**。默认情况下，它们具有最小，首选和灵活大小为0。某些组件在添加到游戏对象时会更改这些布局属性。

图像和文本组件是提供**Layout Element**属性的组件的两个示例。它们更改首选的宽度和高度以匹配精灵或文本内容。

### Layout Element Component
如果要覆盖最小，首选或灵活的大小，可以通过向游戏对象添加**Layout Element**组件来实现。
![](UI_LayoutElementInspector.png)  

**Layout Element**组件使您可以覆盖一个或多个布局属性的值。启用要覆盖的属性的复选框，然后指定要覆盖的值。

有关更多信息，请参见[**Layout Element**](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-LayoutElement.html)的参考页面。

## Understanding Layout Controllers
**Layout Controller**是控制一个或多个布局元素的大小以及可能的位置的组件，这意味着启用Rect Transform的游戏对象。**Layout Controller**可以控制自己的布局元素（与其自身相同的游戏对象），也可以控制子布局元素。

充当**Layout Controller**的组件本身也可以同时充当布局元素。

### Content Size Fitter
**Content Size Fitter**用作布局控制器，用于控制其自身布局元素的大小。观看自动布局系统的最简单方法是将**Content Size Fitter**组件添加到带有**Text**组件的**Game Object**中。

![](UI_ContentSizeFitterInspector.png)

如果将**Horizontal Fit**或**Vertical Fit**设置为**Preferred**，则**Rect Transform**将调整其宽度和/或高度以适合**Text**内容。

有关更多信息，请参见参考页面上的[Content Size Fitter](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-ContentSizeFitter.html)。

### Aspect Ratio Fitter
**Aspect Ratio Fitter**用作布局控制器，可控制其自身布局元素的大小。

![](UI_AspectRatioFitterInspector.png)

它可以调整高度以适合宽度，反之亦然，或者可以使元素适合其父元素或包围其父元素。**Aspect Ratio Fitter**不考虑布局信息，例如最小尺寸和首选尺寸。

有关更多信息，请参见参考页面的**Aspect Ratio Fitter**。

### Layout Groups
**Layout Group**用作控制其子布局元素的大小和位置的布局控制器。例如，**Horizontal Layout Group**将其子级并列放置在一起，而**Grid Layout Group**将其子级放置在网格中。

**Layout Group**无法控制自己的大小。相反，它本身是一个布局元素，可以由其他布局控制器控制或手动设置。

无论分配了**Layout Group**大小如何，在大多数情况下，它将根据报告的最小，首选和灵活大小为其每个子布局元素分配适当的空间。**Layout Group**也可以以这种方式任意嵌套。

有关更多信息，请参见参考页面中的[Horizontal Layout Group](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-HorizontalLayoutGroup.html), [Vertical Layout Group](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-VerticalLayoutGroup.html) 和 [Grid Layout Group](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-GridLayoutGroup.html)。

### Driven Rect Transform properties
由于自动布局系统中的布局控制器可以自动控制某些UI元素的大小和位置，因此不应同时通过检查器或“场景视图”手动编辑这些大小和位置。无论如何，这种更改后的值将仅由布局控制器在下一次布局计算中重置。

**Rect Transform**具有**driven properties**的概念来解决此问题。例如，将**Horizontal Fit**属性设置为**Minimum**或**Preferred**的**Content Size Fitter**将驱动同一游戏对象上Rect Transform的宽度。宽度将显示为只读，并且Rect Transform顶部的小信息框将通知一个或多个属性由**Conten Size Fitter**驱动。

除了阻止手动编辑外，驱动的Rect Transform属性还有其他原因。仅通过更改游戏视图的分辨率或大小即可更改布局。反过来，这可以更改布局元素的大小或位置，从而更改驱动属性的值。但是，仅由于调整了游戏视图的大小，将场景标记为具有未保存的更改是不理想的。为避免这种情况，驱动属性的值不会保存为场景的一部分，对其进行更改也不会将场景标记为已更改。

## Technical Details
自动布局系统内置了某些组件，但也可以创建新组件以自定义方式控制布局。这是通过使组件实现由自动布局系统识别的特定接口来完成的。

### Layout Interfaces
如果组件实现接口**ILayoutElement**，则该组件将被自动布局系统视为布局元素。

如果组件实现接口**ILayoutGroup**，则该组件将驱动其子级的Rect Transform。

如果组件实现了**ILayoutSelfController**接口，则该组件有望驱动自己的Rect Transform。

### Layout Calculations
自动版式系统按以下顺序评估和执行版式：
1. 布局元素的最小，首选和灵活宽度是通过在**ILayoutElement**组件上调用**CalculateLayoutInputHorizo​​ntal**来计算的。这是按照自下而上的顺序执行的，在此顺序中，子节点是在父母之前计算的，因此父母可以在自己的计算中考虑子节点的信息。
2. 布局元素的有效宽度是通过在**ILayoutController**组件上调用**SetLayoutHorizo​​ntal**来计算和设置的。这是按照自上而下的顺序执行的，其中子项是在其父项之后计算的，因为子项宽度的分配需要基于父项中可用的全部宽度。在此步骤之后，布局元素的Rect Transform将具有新的宽度。
3. 布局元素的最小，首选和灵活高度是通过在**ILayoutElement**组件上调用**CalculateLayoutInputVertical**来计算的。这是按照自下而上的顺序执行的，在此顺序中，子节点是在父母之前计算的，因此父母可以在自己的计算中考虑子节点的信息。
4. 布局元素的有效高度是通过在**ILayoutController**组件上调用**SetLayoutVertical**来计算和设置的。这是按照自上而下的顺序执行的，其中子节点是在其父项之后计算的，因为子节点高度的分配需要基于父项中可用的全部高度。在此步骤之后，布局元素的Rect Transform将具有新的高度。

从上面可以看出，自动布局系统首先评估宽度，然后再评估高度。因此，计算出的高度可能取决于宽度，但是计算出的宽度永远不会取决于高度。

### Triggering Layout Rebuild
当组件上的属性更改（这可能导致当前布局不再有效）时，需要重新计算布局。这可以通过调用来触发：
```cs
LayoutRebuilder.MarkLayoutForRebuild（ransform as RectTransform）;
```
重建不会立即发生，而是在当前帧的结尾，即在渲染发生之前。之所以不立即，是因为这将导致布局可能在同一帧中多次重建，这会对性能造成不利影响。

何时触发重建的准则：
* 在可以更改布局的属性的设置器中。
* 在这些回调中：
  * OnEnable
  * OnDisable
  * OnRectTransformDimensionsChange
  * OnValidate (only needed in the editor, not at runtime)
  * OnDidApplyAnimationProperties