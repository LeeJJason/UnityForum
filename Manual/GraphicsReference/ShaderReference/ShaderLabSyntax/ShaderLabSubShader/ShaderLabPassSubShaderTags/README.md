## ShaderLab: SubShader Tags
Subshaders 使用 tags 来告诉渲染引擎如何被渲染。

### Syntax
```
Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```
指定 **TagName1** 的值 **Value1**, **TagName2** 的值 **Value2**。你想要好多就有好多。

### Details
Tags 是基础的键值对。[SubShader](../README.md)内部的tag用于决定渲染顺序和其他subshader的参数。注意，下面的tag只有在SubShader中且不能在Pass中，才能被unity识别。

除了内置的被unity识别的标签外，你能使用自己的，并且使用[Material.GetTag](https://docs.unity3d.com/ScriptReference/Material.GetTag.html)函数查询。

#### Rendering Order - Queue tag
你能使用 Queue tag 决定对象的绘制顺序。一个shader决定他的对象数序哪个 render queue，这种方式，任意的透明shader确保他们在 opaque 对象后面渲染等等。

这儿有4个预定义的render queues，但是在预定义间能有更多的队列。预定义队列如下：
* `Background ` - 该队列比其他队列先渲染。你通常在那些需要在最后面的物体上使用它。
* `Geometry `（默认）- 这个用于大多数对象。非透明的图形使用该队列。
* `AlphaTest ` - 透明测试图形使用这个队列。它是一个独立于Geometry的队列，因为在绘制完所有实体对象之后，渲染经过alpha测试的对象更有效。
* `Transparent` - 这个渲染队列是在几何体和AlphaTest之后按前后顺序渲染的。任何alpha混合(即着色器不写入深度缓冲)应该到这里(玻璃，粒子效果)。
* `Overlay` - 这个渲染队列用于叠加效果。最后渲染的任何东西都应该放到这里(比如镜头闪光)。

```cs
Shader "Transparent Queue Example"
{
     SubShader
     {
        Tags { "Queue" = "Transparent" }
        Pass
        {
            // rest of the shader body...
        }
    }
}
```
*一个演示如何在透明队列中呈现某些内容的示例*

对于特殊用途，可以使用中间队列。在内部，每个队列由整数索引表示;`Background`是1000，`Geometry`是2000，`AlphaTest`是2450，`Transparent`是3000，`Overlay`是4000。如果一个着色器使用队列如下:
```
Tags { "Queue" = "Geometry+1" }
```

这将使对象在所有不透明对象之后渲染，但在透明对象之前渲染，因为渲染队列索引将是2001(几何+ 1)。这在您希望某些对象总是在其他对象集之间绘制的情况下非常有用。例如，在大多数情况下，透明的水应该画在不透明的物体之后，但在透明物体之前。

队列最大到 2500 (“Geometry+500”)被认为是不透明并且优化对象的绘制顺序来获得最好的性能。再高的渲染队列被认为是 “transparent objects” ，并且通过距离排序，按着从最远到最近的顺序开始渲染。Skyboxes 被渲染在非透明和透明对象之间。

### RenderType tag
`RenderType`将shader分类成几个预定义组，比如，一个非透明shader，或者一个alpha-tested shader 等等。这被
[Shader Replacement](../../../AdvancedShaderLabTopics/RenderingWithReplacedShaders/README.md)使用和在一些情况下用于产生[camera’s depth texture](../../../AdvancedShaderLabTopics/CameraDepthTexture/README.md)。

### DisableBatching tag
一些shader（大部分是做 object-space 变形）在[Draw Call Batching](https://docs.unity3d.com/Manual/DrawCallBatching.html)使用时无法正常工作 - 那是因为批处理将所有图形转换到世界空间中，所以 “物体空间” 丢失了。
`DisableBatching` tag 能用于指定这个。这儿有三种可能的值： “True” (总是禁用shader的批处理)， “False” (默认值，不禁用批处理) 和 “LODFading” (LOD fading 激活时关闭批处理，主要用于树木)。

### ForceNoShadowCasting tag
`ForceNoShadowCasting` tag 如果被设置为 “True”，那么使用这个 subshader 的对象将不会投射阴影。这是最有用的，当你使用着色器替代透明的对象，你不想要从另一个子着色器继承一个阴影 pass 。

### IgnoreProjector tag
`IgnoreProjector` tag 如果被设置为 “True”，那么使用这个 shader 的对象将不会被 [Projectors](../../../../VisualEffectsReference/Projector/README.md) 影响。

### CanUseSpriteAtlas tag
`CanUseSpriteAtlas` 设置为 “False” ，如果该shader用于**sprites**，当他们被打成图集时不能正常工作（参考 [Sprite Packer](https://docs.unity3d.com/Manual/SpritePacker.html)）。

### PreviewType tag
`PreviewType`指示材质 inspector 预览应该如何显示材质。默认材质显示为球体，但PreviewType也可以设置为“Plane”(显示为2D)或“Skybox”(显示为Skybox)。

## See Also
Passes 也能设置 Tags，参考[Pass Tags](../ShaderLabPass/ShaderLabPassTags/README.md)。