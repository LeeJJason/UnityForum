## Unity’s Rendering Pipeline
Shaders 定义了一个物体本身的样子(它的材质属性)和它对光线的反应。因为光照计算必须内置到着色器中，并且有许多可能的光和阴影类型，编写高质量的起作用的shaders一个复杂的任务。为了使之更容易，Unity 有 [Surface Shaders](../../WritingSurfaceShaders/README.md)，其中所有的照明，阴影，光照映射，正向和递延渲染事情都是自动处理的。

这个文档描述了Unity的 lighting & rendering pipeline 的细节和[Surface Shaders](../../WritingSurfaceShaders/README.md)幕后发生的事情。

### Rendering Paths
光照如何应用和shader 的哪些[Passes](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/README.md)，取决于使用哪个[Rendering Path](https://docs.unity3d.com/Manual/RenderingPaths.html)。shader中的每个pass与光照类型的联系是通过[Pass Tags](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/ShaderLabPassTags/README.md)确定的。

* [Forward Rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html)中，使用  `ForwardBase` 和 `ForwardAdd` Pass。
* [Deferred Shading](https://docs.unity3d.com/Manual/RenderTech-DeferredShading.html)中，使用  `Deferred ` Pass。
* [legacy Deferred Lighting](https://docs.unity3d.com/Manual/RenderTech-DeferredLighting.html)中，使用  `PrepassBase` 和 `PrepassFinal` Pass。
* [Forward Rendering](https://docs.unity3d.com/Manual/RenderTech-VertexLit.html)中，使用  `Vertex`、`VertexLM` 和 `VertexLMRGBM` Pass。
* 上的的所有，要是渲染[Shadows](https://docs.unity3d.com/Manual/ShadowOverview.html)和深度纹理，使用`ShadowCaster `。

### Forward Rendering path
`ForwardBase` Pass 同时渲染  ambient, lightmaps, main directional light 和 不重要的 (vertex/SH) lights。`ForwardAdd` Pass 用于处理额外的 per-pixel lights；每个被这样的光照射的对象都调用一次。详情请参阅[Forward Rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html)。
如果使用前向渲染，但着色器没有适合前向的pass(即既不存在`ForwardBase`Pass，也不存在`ForwardAdd`Pass)，那么该对象就像在Vertex Lit path中一样被渲染，如下所示。

### Deferred Shading path
`Deferred ` Pass渲染光照需要的所有信息（在内置shader中：diffuse color, specular color, smoothness, world space normal, emission）。它也添加lightmaps, reflection probes 和 ambient lighting 到 emission channel。详情请参阅[Deferred Shading](https://docs.unity3d.com/Manual/RenderTech-DeferredShading.html)。

### Legacy Deferred Lighting path
`PrepassBase` Pass渲染normals & specular 指数；`PrepassFinal` Pass通过结合textures, lighting & emissive 材质属性渲染最终颜色。所有在场景中的普通光照在 screen-space 中分开处理。详情请参阅[legacy Deferred Lighting](https://docs.unity3d.com/Manual/RenderTech-DeferredLighting.html)。

### Legacy Vertex Lit Rendering path
由于vertex lighting最常用于不支持可编程着色器的平台上，Unity不能在内部创建多个着色器变体来处理光照映射和非光照映射的情况。因此，为了处理lightmapping和非lightmapping对象，必须显式地编写多个passes。
* `Vertex` Pass 用于 non-lightmapped 对象。所有的光源同时渲染，使用一个固定的 OpenGL/Direct3D lighting model 函数 ([Blinn-Phong](http://en.wikipedia.org/wiki/Blinn-Phong_shading))。
* `VertexLMRGBM` Pass用于 lightmapped 对象，当 lightmaps 是RGBM编码时 (PC and consoles)。没有时时光照，Pass用于结合纹理与lightmap。
* `VertexLM` Pass用于 lightmapped 对象，当 lightmaps 是double-LDR编码时 (mobile platforms)。没有时时光照，Pass用于结合纹理与lightmap。

## See Also
* [Graphics Command Buffers](https://docs.unity3d.com/Manual/GraphicsCommandBuffers.html) 介绍如何扩展 Unity’s rendering pipeline。