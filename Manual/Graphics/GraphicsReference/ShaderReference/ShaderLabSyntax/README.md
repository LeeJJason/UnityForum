## ShaderLab Syntax
Unity中的所有[Shaders](../ShaderAssets/README.md)文件都是用一种叫做“**ShaderLab**”的声明性语言编写的。在该文件中，嵌套大括号语法声明了描述着色器的各种东西——例如，材质检查器应该显示哪些着色器属性;要做什么样的硬件回退;使用何种混合模式等;而实际的“着色器代码”是在同一个着色器文件内的`CGPROGRAM`代码片段中编写的(参见[表面着色器](../WritingSurfaceShaders/README.md)和[顶点和片段着色器](../WritingVertexAndFragmentShaders/README.md))。

此页面及其子页面描述了嵌套括号“ ShaderLab”的语法。该CGPROGRAM片段写在常规HLSL / CG着色语言，查看到[他们的文档页面](../WritingVertexAndFragmentShaders/README.md)。

**Shader**是着色器文件的根命令。每个文件必须定义一个（也只有一个）Shader。它指定材质使用这个着色器的对象如何被渲染。

## Syntax
```
Shader "name" 
{ 
    [Properties] 
    __Subshaders__ 
    [Fallback] 
    [CustomEditor] 
}
```
定义了一个着色器。它将出现在材质inspector名单下的名字。着色器可选地定义在材质检查器中显示的属性列表。在此之后是子着色器列表，以及可选的回退和/或自定义编辑器声明。

## Details
### Properties
着色器可以具有[属性](ShaderLabProperties/README.md)列表。在Unity中着色器中声明的所有属性都显示在[material inspector](https://docs.unity3d.com/Manual/class-Material.html)。典型属性是对象颜色，纹理或着色器要使用的任意值。

### SubShaders & Fallback
每个着色器均包含一系列[sub-shaders](ShaderLabSubShader/README.md)。您必须至少有一个。加载着色器时，Unity将遍历子着色器列表，并选择终端用户计算机支持的第一个着色器。如果不支持任何子着色器，Unity将尝试使用[fallback shader](ShaderLabFallback/README.md)。

不同的图形卡具有不同的功能。这给游戏开发者提出了一个永恒的问题;你希望你的游戏在最新的硬件上看起来很棒，但不希望它只对3%的人开放。这就是子着色器的用武之地。创建一个subshader，拥有所有你可以想象到的图形效果，然后为旧卡添加更多的subshader。这些子着色器可能以一种较慢的方式实现您想要的效果，或者它们可能选择不实现某些细节。

着色器“level of detail”(LOD)和“shader replacement”是两个建立在子着色器上的技术，详细信息参见[Shader LOD](https://docs.unity3d.com/Manual/SL-ShaderLOD.html)和[Shader Replacemement](https://docs.unity3d.com/Manual/SL-ShaderReplacement.html)。

## Examples
这是最简单的着色器之一：
```
// colored vertex lighting
Shader "Simple colored lighting"
{
    // a single color property
    Properties {
        _Color ("Main Color", Color) = (1,.5,.5,1)
    }
    // define one subshader
    SubShader
    {
        // a single pass in our subshader
        Pass
        {
            // use fixed function per-vertex lighting
            Material
            {
                Diffuse [_Color]
            }
            Lighting On
        }
    }
}
```
此着色器定义了颜色属性**_Color**（在材质检查器中显示为“ 主色”），默认值为（1,0.5,0.5,1）。然后定义一个子着色器。子着色器由一个[Pass](ShaderLabSubShader/ShaderLabPass/README.md)组成，该Pass打开固定功能的顶点照明并为其设置基本材料。

请参见[Surface Shader Examples](../WritingSurfaceShaders/SurfaceShaderExamples/README.md)或[Vertex and Fragment Shader Examples](../WritingVertexAndFragmentShaders/VertexAndFragmentShaderExamples/README.md)中的更复杂的示例。

## See Also
* [Properties Syntax](ShaderLabProperties/README.md)
* [SubShader Syntax](ShaderLabSubShader/README.md)
  * [Pass Syntax](ShaderLabSubShader/ShaderLabPass/README.md)
  * [UsePass Syntax](ShaderLabSubShader/ShaderLabUsePass/README.md)
  * [GrabPass Syntax](ShaderLabSubShader/ShaderLabGrabPass/README.md)
  * [Tags Syntax](ShaderLabSubShader/ShaderLabPassSubShaderTags/README.md)
* [Fallback Syntax](ShaderLabFallback/README.md)
* [CustomEditor Syntax](ShaderLabCustomEditor/README.md)
* [Other commands](ShaderLabOtherCommands/README.md)