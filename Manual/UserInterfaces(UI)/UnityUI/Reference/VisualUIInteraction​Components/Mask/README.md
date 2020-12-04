# [Mask](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Mask.html)
Mask不是可见的UI控件，而是一种修改控件的子元素外观的方法。 遮罩将子元素限制（即“遮罩”）为父元素的形状。 因此，如果孩子比父母大，那么只有适合父节点内部的子节点的部分才可见。  
![](MaskCtrlExample.png)

## Properties
![](UI_MaskInspector.png)

|Property:|Function:
|:--------|:-------
|Show Graphic|遮罩（父）对象的图形是否应在子对象上绘制Alpha？

## Description
遮罩的常见用法是显示大型图像的一小部分，例如使用Panel对象（菜单：**GameObject> Create UI> Panel**）作为“帧”。 您可以通过首先将Image作为Panel对象的子级来实现。 您应该放置图像的位置，以使应显示的区域直接位于“面板”区域的后面。

然后，将“遮罩”组件添加到面板中。 子图像在面板外部的区域将变得不可见，因为它们被面板的形状遮盖了。  
![](MaskEnabled.svg)

如果随后移动图像，则只有面板显示的部分可见。 可以通过[Scrollbars](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Scrollbar.html)控制移动，从而为地图创建可滚动查看器。

## Implementation
使用GPU的模板缓冲区实现屏蔽。

* 第一个Mask元素向模板缓冲区写入1 
* 渲染时会检查掩模下面的所有元素，并且仅渲染到模板缓冲区中存在1的区域
* Nested Masks将增量位掩码写入缓冲区，这意味着 可渲染的子代需要具有逻辑＆的模板值才能渲染。