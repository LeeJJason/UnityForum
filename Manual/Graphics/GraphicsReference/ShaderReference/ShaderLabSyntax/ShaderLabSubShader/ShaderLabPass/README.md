## ShaderLab: Pass
Pass 块使 **GameObject** 的几何图元被渲染一次。

### Syntax
```
Pass { [Name and Tags] [RenderSetup] }
```
基础的 Pass 命令包含一系列的渲染状态设置命令。

### Name and tags
一个Pass可以定义它的 [Name](ShaderLabName/README.md)和任意数量的[Tags](ShaderLabPassTags/README.md)。这些是名称/值字符串，用于将Pass的意图传达给渲染
引擎。

### Render state set-up
一个Pass设置图形硬件的各种状态，比如是否启用 alpha blending 或者使用 depth testing。
这些命令如下：

#### Cull
```
Cull Back | Front | Off
```
设置多边形的裁剪模式。

#### ZTest
```
ZTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always)
```
设置**depth buffer**的测试模式。

#### ZWrite
```
ZWrite On | Off
```
设置**depth buffer**的写入模式。


#### Offset
```
Offset OffsetFactor, OffsetUnits
```
设置 Z buffer 深度偏移。有关更多详细信息，请参见[Cull and Depth page](ShaderLabCullingDepthTesting/README.md)
有关Cull，ZTest，ZWrite和Offset的更多详细信息，请参见有关[Cull and Depth](ShaderLabCullingDepthTesting/README.md)文档。

#### Blend
```
Blend sourceBlendMode destBlendMode
Blend sourceBlendMode destBlendMode, alphaSourceBlendMode alphaDestBlendMode
BlendOp colorOp
BlendOp colorOp, alphaOp
AlphaToMask On | Off
```
设置 alpha blending, alpha operation, 和 alpha-to-coverage模式。有关更多详细信息，请参见关于[Blending](ShaderLabBlending/README.md)文档。

#### ColorMask
```
ColorMask RGB | A | 0 | any combination of R, G, B, A
```
设置颜色通道书写蒙版。编写ColorMask 0将关闭对所有颜色通道的渲染。默认模式是写入所有通道（RGBA），但是对于某些特殊效果，您可能希望保留某些通道不变或完全禁用颜色写入。

使用多个渲染目标（MRT）渲染时，可以通过在末尾添加索引（0-7）为每个渲染目标设置不同的颜色蒙版。例如，`ColorMask RGB 3`将使渲染目标3仅写入RGB通道。

### Legacy fixed-function Shader commands
许多命令用于编写遗留的 “fixed-function style” **Shaders**。这是被认为遗弃的功能，因为编写[Surface Shaders](https://docs.unity3d.com/Manual/SL-SurfaceShaders.html)
或者[Shader programs](https://docs.unity3d.com/Manual/SL-ShaderPrograms.html) 允许更多的灵活性。但是，对于非常简单的着色器，有时以固定功能样式编写它们会更容易，因此此处提供了命令。请注意，如果不使用固定功能着色器，则将忽略以下所有命令。

#### Fixed-function Lighting and Material
```
Lighting On | Off
Material { Material Block }
SeparateSpecular On | Off
Color Color-value
ColorMaterial AmbientAndDiffuse | Emission
```
所有这些都控制每个顶点的固定功能光照:它们打开它，设置材质颜色，打开高光，提供默认颜色(如果顶点光照关闭)，并控制网格的方式顶点颜色影响光照。参见[Materials](https://docs.unity3d.com/Manual/SL-Material.html)文档为更多的细节。

#### Fixed-function Fog
```
Fog { Fog Block }
```
设置fixed-function Fog参数。有关更多详细信息，请参见有关[Fogging](ShaderLabLegacyFog/README.md)的文档。

#### Fixed-function AlphaTest
```
AlphaTest (Less | Greater | LEqual | GEqual | Equal | NotEqual | Always) CutoffValue
```
打开fixed-function alpha testing。有关更多详细信息，请参见有关[alpha testing](ShaderLabLegacyAlphaTesting/README.md)的文档。

#### Fixed-function Texture combiners
设置渲染状态后，请使用[SetTexture](ShaderLabLegacyTextureCombiners/README.md)命令指定许多“纹理”及其组合模式：
```
SetTexture textureProperty { combine options }
```

## Details
着色通道与Unity的渲染管道以多种方式交互;例如，一个Pass可以指示它应该只用于[deferred shading](https://docs.unity3d.com/Manual/RenderTech-DeferredShading.html)
使用[Tags](ShaderLabPassTags/README.md)命令。某些Pass也可以在同一个GameObject上执行多次;例如，在[forward rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html)中“ForwardAdd”的Pass类型会根据影响游戏对象的灯光数量被执行多次。有关更多细节，请参阅[Render Pipeline](https://docs.unity3d.com/Manual/SL-RenderPipeline.html)的文档。

## See also
有几种特殊的Pass可用于重用常用功能或实现各种高端效果：
* [UsePass](../ShaderLabUsePass/README.md)包括来自另一个着色器的命名通道。
* [GrabPass](../ShaderLabGrabPass/README.md)将屏幕上的内容抓取到Texture中，以供以后使用。