# [RectMask2D](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-RectMask2D.html)
RectMask2D是类似于“遮罩”控件的遮罩控件。 遮罩将子元素限制为父元素的矩形。 与标准“蒙版”控件不同，它具有一些局限性，但也具有许多性能优势

## Description
RectMask2D的常见用法是显示较大区域的小部分。 使用RectMask2D对该区域进行构图。

RectMask2D控件的局限性是：
* 它仅适用于2D空间
* 它将无法正确遮罩不共面的元素

RectMask2D的优点是：
* 它不使用模板缓冲区
* 没有额外的draw calls
* 无材质变化
* 很快的性能