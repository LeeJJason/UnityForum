# [Image](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Image.html)
Image控件向用户显示非交互式图像。 您可以将其用于装饰或图标等目的，还可以从脚本更改图像以反映其他控件中的更改。 该控件与[Raw Image](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-RawImage.html)控件类似，但提供了更多的选项来设置图像动画效果并准确填充控件矩形。 但是，Image控件要求其Texture为[Sprite](https://docs.unity3d.com/Manual/class-TextureImporter.html)，而Raw Image可以接受任何Texture。  
![](ImageCtrlExample.png)

## Properties
![](UI_ImageInspector.png)

|Property:|Function:
|:--------|:--------
|Source Image|表示要显示的图像的Texture（必须作为[Sprite](https://docs.unity3d.com/Manual/class-TextureImporter.html)导入）。
|Color|应用于图像的颜色。
|Material|用于渲染图像的材质。
|Raycast Target|如果您希望Unity将图像视为**Raycast Target**，请启用**Raycast Target**。
|Preserve Aspect|确保图像保留其现有尺寸。
|Set Native Size|将图像框的尺寸设置为“纹理”的原始像素大小。

您必须导入图像以显示为Sprite才能使用Image控件。