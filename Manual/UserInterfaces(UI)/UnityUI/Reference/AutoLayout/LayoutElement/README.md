# [Layout Element](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-LayoutElement.html)
如果要覆盖布局元素的 minimum, preferred 或 flexible 大小，可以通过向GameObject添加一个Layout Element组件来实现。

布局控制器按以下顺序为布局元素分配宽度或高度：
* 首先，布局控制器分配最小尺寸属性（**Min Width, Min Height**）。
* 如果有足够的可用空间，则布局控制器将分配首选尺寸属性（**Preferred Width, Preferred Height**）。
* 如果有其他可用空间，则布局控制器将分配弹性尺寸属性（**Flexible Width, Flexible Height**）。

有关最小，首选和灵活尺寸的更多信息，请参见[Auto Layout](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UIAutoLayout.html)文档。

## Properties
![](UI_LayoutElementInspector.png)

启用width或height属性时，其旁边将显示一个值字段。 使用此值字段输入宽度或高度的确切值。 **Min** 和 **Preferred** 尺寸以常规单位表示，而 **Flexible** 尺寸以相对单位表示。
|Property:|Function:
|:--------|:----------
|Ignore Layout|启用后，布局系统将忽略此布局元素。
|Min Width|此布局元素应具有的最小宽度。
|Min Height|此布局元素应具有的最小高度。
|Preferred Width|在分配其他可用宽度之前，此布局元素应具有的首选宽度。
|Preferred Height|在分配其他可用高度之前，此布局元素应具有的首选高度。
|Flexible Width|此布局元素应相对于其同级元素填充的额外可用宽度的相对数量。
|Flexible Height|此布局元素应相对于其同级元素填充的额外可用高度的相对数量。
|Layout Priority|该组件的布局优先级。</br>如果GameObject具有一个以上具有布局属性的组件（例如Image组件和LayoutElement组件），则布局系统将使用具有最高**Layout Priority**的组件的属性值 。</br>如果组件具有相同的**Layout Priority**，则布局系统将为每个属性使用最大值，而不管其来自哪个组件。

## Description
布局元素组件使您可以覆盖一个或多个布局属性的值。 启用要覆盖的属性的复选框，然后指定要覆盖的值。

最小和首选尺寸以常规单位定义，而弹性尺寸以相对单位定义。 如果任何布局元素的灵活尺寸都大于零，则意味着将填充所有可用空间。 兄弟姐妹的相对灵活大小值确定每个兄弟姐妹占可用空间的比例。 最常见的是，将灵活的宽度和高度设置为0或1。

在某些情况下，同时指定首选大小和灵活大小可能很有意义。 仅在所有首选大小都已完全分配后才分配灵活大小。 因此，具有指定的灵活大小但没有首选大小的布局元素将保持其最小大小，直到其他布局元素已增长到其完整的首选大小，然后才基于其他可用空间开始增长。 通过还指定灵活的大小，可以避免这种情况，并且元素可以与具有首选大小的其他布局元素一起增长到其首选大小，然后在分配了所有灵活大小后进一步增长。