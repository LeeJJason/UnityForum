## ShaderLab: Properties
着色器可以定义由艺术家在Unity的[material inspector](https://docs.unity3d.com/Manual/Materials.html)中设置的参数列表。[shader file](../README.md)中的Properties块定义了它们。

## Syntax
### Properties
```
Properties { Property [Property ...] }
```
定义属性块。括号内的多个属性定义如下。

### Numbers and Sliders
```
name ("display name", Range (min, max)) = number
name ("display name", Float) = number
name ("display name", Int) = number
```
这些都定义了带有默认值的数字（标量）属性。该`Range`形式使它显示为最小和最大范围之间的滑块。

### Colors and Vectors
```
name ("display name", Color) = (number,number,number,number)
name ("display name", Vector) = (number,number,number,number)
```
用给定RGBA组件的默认值定义颜色属性，或用默认值定义4D向量属性。颜色属性有一个颜色选择器，根据需要根据颜色空间进行调整(参见[Properties in Shader Programs](../../WritingVertexAndFragmentShaders/AccessingShaderPropertiesInCgHLSL/README.md))。向量属性显示为四个数字字段。

### Textures
```
name ("display name", 2D) = "defaulttexture" {}
name ("display name", Cube) = "defaulttexture" {}
name ("display name", 3D) = "defaulttexture" {}
```
分别定义了 [2D texture](https://docs.unity3d.com/Manual/class-TextureImporter.html), [cubemap](https://docs.unity3d.com/Manual/class-Cubemap.html) 和 [3D (volume)](https://docs.unity3d.com/Manual/class-Texture3D.html)

## Details
在**shader**中的属性通过**name**(在Unity,属性名字通常以下划线开始)引用。属性在材质**inspector**中以**display name**显示。每个属性在等号后会给出默认值：
* 对于Range和Float属性，它只是一个数字，例如“ 13.37”。
* 对于“ 颜色”和“ 矢量”属性，它是括号中的四个数字，例如“（1,0.5,0.2,1）”。
* 对于2D纹理，默认值为空字符串或内置的默认纹理之一：“白色”（RGBA：1,1,1,1），“黑色”（RGBA：0,0,0， 0），“灰色”（RGBA：0.5、0.5、0.5、0.5），“凹凸”（RGBA：0.5、0.5、1、0.5）或“红色”（RGBA：1、0、0、0）。
* 对于非2D纹理（Cube，3D，2DArray），默认值为空字符串。如果没有为材质指定Cubemap / 3D / Array Texture，则使用灰色的（RGBA：0.5,0.5,0.5,0.5）。

稍后，在着色器的固定函数部分中，可以使用方括号中的属性名[name]访问属性值。例如，您可以通过声明两个整数属性(如“SrcBlend”和“DstBlend”)，使混合模式由material属性驱动，然后使用 [Blend Command](https://docs.unity3d.com/Manual/SL-Blend.html):`Blend [_SrcBlend] [_DstBlend]`。
属性块中的着色器参数被序列化为[材质数据](https://docs.unity3d.com/Manual/Materials.html)。[着色器程序](https://docs.unity3d.com/Manual/SL-ShaderPrograms.html)实际上可以有更多的参数(如矩阵、向量和浮点数)，这些参数在运行时由代码在材质上设置，但是如果它们不是属性块的一部分，那么它们的值将不会被保存。这对于完全由脚本代码驱动的值非常有用([Material.SetFloat](https://docs.unity3d.com/ScriptReference/Material.SetFloat.html) 和类似的函数)。

## Property attributes and drawers
在任何属性的前面，都可以在方括号中指定可选属性。这些是Unity可以识别的属性，或者它们可以指示您自己的[MaterialPropertyDrawer类](https://docs.unity3d.com/ScriptReference/MaterialPropertyDrawer.html)，以控制应如何在[material inspector](https://docs.unity3d.com/Manual/class-Material.html)中呈现它们。Unity可以识别的属性：
* [HideInInspector] - 在material inspector中不显示属性值。
* [NoScaleOffset] -material inspector不会显示具有此属性的纹理属性的纹理平铺/偏移字段。
* [Normal] -表示纹理属性需要法线贴图。
* [HDR] -表示纹理属性期望高动态范围（HDR）纹理。
* [Gamma]-表示在用户界面中将float / vector属性指定为sRGB值，（就像颜色一样），并且可能需要根据使用的颜色空间进行转换。请参见[Properties in Shader Programs](../../WritingVertexAndFragmentShaders/AccessingShaderPropertiesInCgHLSL/README.md)。
* [PerRendererData]-表示纹理属性将以[MaterialPropertyBlock](https://docs.unity3d.com/ScriptReference/MaterialPropertyBlock.html)的形式来自每个渲染器数据。材质检查器更改了这些属性的纹理插槽UI。

## Example
```
// properties for a water shader
Properties
{
    _WaveScale ("Wave scale", Range (0.02,0.15)) = 0.07 // sliders
    _ReflDistort ("Reflection distort", Range (0,1.5)) = 0.5
    _RefrDistort ("Refraction distort", Range (0,1.5)) = 0.4
    _RefrColor ("Refraction color", Color) = (.34, .85, .92, 1) // color
    _ReflectionTex ("Environment Reflection", 2D) = "" {} // textures
    _RefractionTex ("Environment Refraction", 2D) = "" {}
    _Fresnel ("Fresnel (A) ", 2D) = "" {}
    _BumpMap ("Bumpmap (RGB) ", 2D) = "" {}
}
```

## Texture property options (removed in 5.0)
在Unity 5之前，纹理属性可以在花括号块内具有选项，例如TexGen CubeReflect。这些控制固定功能纹理坐标的生成。在5.0中已删除此功能；如果需要texgen，则应编写一个顶点着色器。有关示例，请参见实现固定功能的TexGen页面。

## See Also
* [Accessing Shader Properties in HLSL/Cg](../../WritingVertexAndFragmentShaders/AccessingShaderPropertiesInCgHLSL/README.md)
* [Material Property Drawers](https://docs.unity3d.com/ScriptReference/MaterialPropertyDrawer.html)