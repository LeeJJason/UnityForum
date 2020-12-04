# [Raw Image](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-RawImage.html)
Raw Image控件向用户显示非交互式图像。 您可以将其用于装饰或图标等目的，还可以从脚本更改图像以反映其他控件中的更改。 该控件与[Image](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Image.html)控件相似，但提供了更多的动画图像选项和准确填充控件矩形的选项。 但是，Image控件要求其Texture为Sprite，而Raw Image可以接受任何Texture。  
![](RawImageCtrlExample.png)

## Properties
![](UI_RawImageInspector184.png)

|Property:|Function:
|:--------|:--------
|Texture|表示要显示的图像的纹理。
|Color|应用于图像的颜色。
|Material|用于渲染图像的材质。
|Raycast Target|如果您希望Unity将图像视为光线投射的目标，请启用光线投射目标。
|UV Rectangle|图像在控制矩形内的偏移量和大小，以标准化坐标（范围为0.0到1.0）给出。 图像的边缘被拉伸以填充UV矩形周围的空间。

## Details
由于原始图像不需要精灵纹理，因此可以使用它来显示Unity播放器可用的任何纹理。 例如，您可能会显示使用[WWW](https://docs.unity3d.com/ScriptReference/WWW.html.md)类从URL下载的图像或游戏对象的纹理。

UV矩形属性使您可以显示较大图像的一小部分。 X和Y坐标指定图像的哪一部分与控件的左下角对齐。 例如，X坐标0.25将切除图像的最左四分之一。 W和H（即宽度和高度）属性指示将被缩放以适合控件矩形的图像部分的宽度和高度。 例如，宽度和高度为0.5会将图像区域的四分之一缩放到控制矩形。 通过更改这些属性，可以根据需要缩放和缩放图像（另请参见[滚动条控件](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Scrollbar.html)）。