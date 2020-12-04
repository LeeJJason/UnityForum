# [Content Size Fitter](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-ContentSizeFitter.html)

## Properties
![](UI_ContentSizeFitterInspector.png)

|Property:|Function:
|:--------|:------
|Horizontal Fit|如何控制宽度。
|&emsp;Unconstrained|不要根据布局元素来驱动宽度。
|&emsp;Min Size|根据布局元素的最小宽度驱动宽度。
|&emsp;Preferred Size|根据布局元素的首选宽度驱动宽度。
|Vertical Fit|如何控制高度。
|&emsp;Unconstrained|不要根据布局元素来驱动高度。
|&emsp;Min Size|根据布局元素的最小高度驱动高度。
|&emsp;Preferred Size|根据布局元素的首选高度来驱动高度。

## Description
Content Size Fitter用作布局控制器，用于控制其自身布局元素的大小。 大小由游戏对象上的布局元素组件提供的最小或首选大小确定。 此类布局元素可以是 Image 或 Text 组件，layout groups 或 Layout Element 组件。

值得牢记的是，调整Rect Transform的大小时（无论是通过Content Size Fitter还是其他方式），调整大小都是围绕枢轴进行的。 这意味着可以使用枢轴控制调整大小的方向。

例如，当枢轴位于中心时，内容大小拟合器将在所有方向均等地扩展Rect Transform。 并且，当枢轴位于左上角时，内容大小调整器将向右下方扩展Rect Transform。