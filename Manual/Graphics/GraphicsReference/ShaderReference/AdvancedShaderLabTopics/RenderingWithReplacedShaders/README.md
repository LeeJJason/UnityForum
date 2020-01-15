## Rendering with Replaced Shaders
一些渲染特效需要渲染一个场景使用一组不同的着色器。例如，好的边缘检测需要一个具有场景法线的纹理，这样它就可以检测出表面方向不同的边缘。其他效果可能需要一个纹理与场景深度，等等。为了实现这一点，可以使用替换所有对象的着色器来渲染场景。

* 着色器替换是通过使用  [Camera.RenderWithShader](https://docs.unity3d.com/ScriptReference/* Camera.RenderWithShader.html) 或者 [Camera.SetReplacementShader](https://docs.unity3d.com/ScriptReference/Camera.SetReplacementShader.html) 函数完成。这两个函数都有一个 **shader** 和一个 **replacementTag**。

* 它是这样工作的:相机按正常方式渲染场景。对象仍然使用它们的材质，但是最终使用的实际着色器改变了:
* 如果**replacementTag**为空，则使用给定的替换着色器呈现场景中的所有对象。
* * 如果**replacementTag**不是空的，那么对于每个要呈现的对象:
  * 查询真实对象的着色器的[tag value](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPassSubShaderTags/README.md)。
  * 如果没有该标记，则**不呈现对象**。
  * 一个[subshader](../../ShaderLabSyntax/ShaderLabSubShader/README.md)在替换着色器中找到，该着色器具有具有找到的值的给定标记。如果没有找到这样的子着色器，则不呈现对象。
  * 现在subshader被用来渲染对象。
  * 
例如，如果所有的着色器都有一个“RenderType”标签，它的值类似于“”，“Transparent”，“Background”，“Overlay”，你可以编写一个替换的着色器，它只渲染实体对象，使用RenderType= solid的子着色器。在替换着色器中找不到其他标记类型，因此不会呈现对象。或者你可以为不同的“RenderType”标签值写几个子着色器。顺便说一句，所有内置的Unity着色器都有一个“RenderType”标签集。


### Lit shader replacement
当使用着色器替换时，场景是使用相机上配置的渲染路径来渲染的。这意味着用于替换的着色器可以包含阴影和照明通道(您可以使用表面着色器替换着色器)。这对于渲染特殊效果和场景调试非常有用。

### Shader replacement tags in built-in Unity shaders
所有内置的Unity着色器都有一个“**RenderType**”标签集，可以在用替换的着色器渲染时使用。标签值如下:
* Opaque: 大部分shader（[Normal](https://docs.unity3d.com/Manual/shader-NormalFamily.html), [Self Illuminated](https://docs.unity3d.com/Manual/shader-SelfIllumFamily.html), [Reflective](https://docs.unity3d.com/Manual/shader-ReflectiveFamily.html), terrain shaders）。
* Transparent: 大多数半透明着色器 ([Transparent](https://docs.unity3d.com/Manual/shader-TransparentFamily.html), Particle, Font, terrain additive pass shaders)。
* TransparentCutout: 蒙面透明着色器 ([Transparent Cutout](https://docs.unity3d.com/Manual/shader-TransparentCutoutFamily.html), two pass vegetation shaders)。
* Background: **Skybox**着色器。
* Overlay: GUITexture, Halo, Flare 着色器.
* TreeOpaque: terrain engine tree bark.
* TreeTransparentCutout: terrain engine tree leaves.
* TreeBillboard: terrain engine billboarded trees.
* Grass: terrain engine grass.
* GrassBillboard: terrain engine billboarded grass.

### Built-in scene depth/normals texture
相机有一个内置的能力，以渲染depth或者depth+normals纹理，如果你在一些效果过需要。参见[Camera Depth Texture](../CameraDepthTexture/README.md)页面。注意，在某些情况下(取决于硬件)，depth或者depth+normals纹理可以在内部使用着色器替代渲染。所以在着色器中使用正确的“RenderType”标签是很重要的。

## Code Example
你的 start() 函数指定替换的shader
```cs
void Start() {
    camera.SetReplacementShader (EffectShader, "RenderType");
}
```
这要求EffectShader使用RenderType键。EffectShader将为您想要的每种呈现类型提供键-值标记。着色器将看起来像:
```cs
Shader "EffectShader" {
     SubShader {
         Tags { "RenderType"="Opaque" }
         Pass {
             ...
         }
     }
     SubShader {
         Tags { "RenderType"="SomethingElse" }
         Pass {
             ...
         }
     }
 ...
 }
```

SetReplacementShader将遍历场景中的所有对象，并使用第一个具有指定键匹配值的子着色器，而不是使用它们的常规着色器。在这个例子中，任何带有Rendertype=“Opaque”标签的对象将会被EffectShader中的第一个子着色器所代替，任何带有Rendertype= " SomethingElse "着色器的对象将会使用第二个子着色器。任何对象，如果其着色器在替换着色器中没有与指定键匹配的标记值，将不会被呈现。