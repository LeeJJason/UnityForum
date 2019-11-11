## Accessing shader properties in Cg/HLSL
shader在 [Properties](../../ShaderLabSyntax/ShaderLabProperties/README.md) 块中什么材质的属性。如果你想在[shader program](../README.md)访问属性，需要生命一个具有相同名字和类型匹配的 Cg/HLSL 变量。[Shader Tutorial: Vertex and Fragment Programs](https://docs.unity3d.com/Manual/ShaderTut2.html) 中提供了一个例子。

比如这些shader属性：
```
_MyColor ("Some Color", Color) = (1,1,1,1) 
_MyVector ("Some Vector", Vector) = (0,0,0,0) 
_MyFloat ("My float", Float) = 0.5 
_MyTexture ("Texture", 2D) = "white" {} 
_MyCubemap ("Cubemap", CUBE) = "" {} 
```
应该在Cg/HLSL代码中生命用于访问：
```
fixed4 _MyColor; // low precision type is usually enough for colors
float4 _MyVector;
float _MyFloat; 
sampler2D _MyTexture;
samplerCUBE _MyCubemap;
```
Cg/HLSL也支持**uniform**关键字，但是不是没必要：
```
uniform float4 _MyColor;
```
ShaderLab的属性类型映射到 Cg/HLSL 变量类型遵循：
* Color 和 Vector 属性映射到 float4, half4 或者 fixed4 变量.
* Range 和 Float 属性映射到 float, half 或者 fixed 变量.
* Texture 属性映射到 sampler2D 变量 for 常规(2D)图片; Cubemaps 映射到 samplerCUBE; 3D 图片映射到 sampler3D.

## How property values are provided to shaders
从以下位置找到shader属性值并将其提供给着色器：
* 在[MaterialPropertyBlock](https://docs.unity3d.com/ScriptReference/MaterialPropertyBlock.html)中设置的每个渲染器值。这是典型的“per-instance”数据（例如，许多共享相同材质的对象的自定义色调颜色）。
* 设置渲染对象使用的[材质](https://docs.unity3d.com/Manual/class-Material.html)中设置值。
* 全局着色器属性，由Unity 渲染设置代码本身设置(参考 [Built-in shader variables](Built-inShaderVariables/README.md))，或者从自己的脚本中(例如 [Shader.SetGlobalTexture](https://docs.unity3d.com/ScriptReference/Shader.SetGlobalTexture.html))。


优先顺序如上所述：按实例数据覆盖所有内容；然后使用材质数据；最后，如果这两个地方都不存在shader属性，则使用全局属性值。最后，如果没有在任何地方定义着色器属性值，则将提供“默认”值（浮点为零，黑色为颜色，空白色为纹理）。

## Serialized and Runtime Material properties
[材质](https://docs.unity3d.com/Manual/class-Material.html)包含序列化的值和运行时设置的值。
序列化数据是所有在[Properties](../../ShaderLabSyntax/ShaderLabProperties/README.md)块中定义的属性。通常，这些字需要存储在材质中，并且能通过材质Inspector调整。
一个材质也能有一些属性在shader中使用，但是没有在[Properties](../../ShaderLabSyntax/ShaderLabProperties/README.md)块中声明。通常，这些属性是在运行时通过脚本代码设置，比如，通过[Material.SetColor](https://docs.unity3d.com/ScriptReference/Material.SetColor.html)。请注意，矩阵和数组只能作为未序列化的运行时属性存在（因为无法在Properties块中定义它们）。

## Special Texture properties
对于设置为着色器/材质属性的每个纹理，Unity还在其他矢量属性中设置了一些额外的信息。

### Texture tiling & offset
[材质](https://docs.unity3d.com/Manual/class-Material.html)的纹理属性通常具有“tiling”和“offset”字段。此信息通过float4 {TextureName}_ST属性传递到着色器中：
* x 包含 X 平铺值
* y 包含 Y 平铺值
* z 包含 X 偏移值
* w 包含 Y 偏移值

例如，如果着色器包含名为_MainTex的纹理，则平铺信息将位于_MainTex_ST矢量中。

### Texture size
{TextureName}_TexelSize - 一个float4属性包含纹理尺寸信息：
* x 包含 1.0/width
* y 包含 1.0/height
* z 包含 width
* w 包含 height

### Texture HDR parameters
{TextureName}_HDR - 一个float4属性，其中包含有关如何解码HDR具体取决于所用的[色彩空间](https://docs.unity3d.com/Manual/LinearLighting.html)，（例如RGBM编码）纹理。请参见[UnityCG.cginc](../Built-inShaderIncludeFiles/README.md)着色器包含文件中的DecodeHDR函数。

## Color spaces and color/vector shader data
当使用[Linear color space,](https://docs.unity3d.com/Manual/LinearLighting.html)时，所有材质颜色属性均以sRGB颜色提供，但在传递到着色器时会转换为线性值。
比如，如果[Properties](../../ShaderLabSyntax/ShaderLabProperties/README.md)块中包含被称为 “MyColor“的`Color `属性，MyColor” 对应的HLSL变量将会转换为线性颜色值。
对于`Float`或者`Vector`类型的属性，默认情况下没有颜色空间的转换;被假定为包含非颜色数据。可以通过给float/vector类型属性添加`[Gamma]`属性指明在sRGB空间，就和颜色一样(参考 [Properties](../../ShaderLabSyntax/ShaderLabProperties/README.md)。

## See Also
* [ShaderLab Properties block](../../ShaderLabSyntax/ShaderLabProperties/README.md)
* [Writing Shader Programs](../README.md)