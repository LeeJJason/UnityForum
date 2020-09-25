## Sprite Atlas
2D项目使用Sprites和其他图形以创建其场景的视觉效果。这意味着一个项目可能包含许多纹理文件。Unity通常会为场景中的每个纹理发出一个[draw call](https://docs.unity3d.com/Manual/DrawCallBatching.html)；但是，在具有许多纹理的项目中，多次绘制调用会占用大量资源，并且会对项目的性能产生负面影响。

一个 Sprite Atlas 是一种资源，它整合多个材质为单一组合纹理。Unity可以调用此单个Texture来发出单个绘制调用，而不是多个绘制调用来以较小的性能开销一次访问所有打包的Textures。此外，[Sprite Atlas API](https://docs.unity3d.com/ScriptReference/U2D.SpriteAtlas.html)可让您控制如何在项目的运行时加载Sprite Atlas。  
![](SpriteAtlasWindow_reupload.png)  
*Sprite Atlas Inspector window*

### Sprite Atlas properties
要创建**Sprite Atlas**，请进入菜单：**Asset > Create > Sprite Atlas**。Unity在Asset文件夹中创建Sprite Atlas，文件扩展名为 *.spriteatlas。

|Property|Description|
|:-------|:----------|
|Type|将“ Sprite Atlas”类型设置为“ Master”或“ Variant”。“主”是默认的“类型”设置。当您将此属性设置为Variant时，Unity将显示其他属性设置。有关这两种类型的更多信息，请参考有关 [Master and Variant Sprite Atlases](https://docs.unity3d.com/Manual/MasterVariantAtlases.html) 的文档。|
|Include in Build|选中此框可将Sprite Atlas资产包括在当前版本中。默认情况下启用此选项。|
|Allow Rotation|选中此框以允许将Unity打包到Sprite Atlas中时Sprite旋转。这样可以最大化组合纹理中Sprite的密度，并且默认情况下启用。如果Sprite Atlas包含[Canvas UI](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UICanvas.html)元素Textures，请禁用此选项，因为当打包时Unity旋转Sprite Atlas中的Textures时，它也会在Scene中旋转其方向。|
|Tight Packing|选中此框可根据Sprite轮廓而不是默认的矩形轮廓打包Sprite。这样可以最大化组合纹理中Sprite的密度，并且默认情况下启用。|
|Padding|定义Sprite Atlas中各个Sprite纹理之间像素数。这是一个缓冲区，用于防止在Sprite Atlas中彼此相邻的Sprite之间发生像素重叠。默认值为4 像素。|
|Read/Write Enabled|选中此框以启用从脚本函数（例如Texture2D.SetPixels和其他Texture2D函数）对Texture数据的访问。如果启用此属性，则Unity将创建纹理数据的副本。这会使纹理资产所需的内存量增加一倍，并且可能会对性能产生负面影响。默认情况下禁用此属性。该属性仅对未压缩或DXT压缩纹理有效，因为Unity无法读取其他类型的压缩纹理。|
|Generate&nbsp;Mip&nbsp;Maps|选中此框以启用Mipmap生成。有关更多信息，请参阅[纹理类型](https://docs.unity3d.com/Manual/TextureTypes.html)的文档。|
|sRGB|	选中此框可将纹理存储在伽玛空间中。有关更多信息，请参考有关[导入纹理](https://docs.unity3d.com/Manual/ImportingTextures.html)的文档。|
|Filter Mode|选择在转换过程中拉伸时，Unity如何过滤打包的纹理。此设置将覆盖Atlas中任何打包Sprite 的**Filter Mode**设置。有关更多信息，请参阅[纹理类型](https://docs.unity3d.com/Manual/TextureTypes.html)的文档。|
|Default&nbsp;(Texture&nbsp;importer&nbsp;settings&nbsp;panel)|设置Sprite Atlas的Texture Importer设置。这将覆盖Sprite Atlas所包含的各个贴图的导入器设置。有关更多信息，请参阅有关 [Texture Importer Override](https://docs.unity3d.com/Manual/class-TextureImporterOverride.html) 的文档。|
|Objects For Packing|Unity将此列表中的所有项目打包到当前选定的Sprite Atlas中。有关更多信息，请参阅有关[Selecting items for the Objects for Packing list](SpriteAtlasWorkflow/README.md) 的文档。|