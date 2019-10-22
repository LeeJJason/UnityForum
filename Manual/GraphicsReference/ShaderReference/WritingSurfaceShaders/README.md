写与光照交互的shader是非常复杂的。这儿需要不同的光源类型、不同的阴影选项，不同的渲染路径 (forward and deferred rendering)，所以shader需要处理所有的复杂性。

Unity中的Surface Shaders是一种代码生成方式，比使用常规的 vertex/pixel shader 更容易实现光照。注意，在Surface Shaders没有自定义语言，魔法或者忍者，它只是生成了所有需要用手写的重复性代码。仍然使用的是HLSL语言。

## 如何工作
定义一个 surface function 用来处理所有的输入数据，然后输出到结构体 SurfaceOutput 中。SurfaceOutput 主要描述了表面的属性 (albedo color, normal, emission, specularity.)。使用HLSL实现代码。

Surface Shader 编译器计算出那些输入是需要的，哪些输出被填充，然后生成实际的  vertex&pixel shaders，和用于处理forward and deferred rendering 的 rendering passes。

surface shaders的标准输出结构体如下：
```
struct SurfaceOutput
{
    fixed3 Albedo;  // diffuse color
    fixed3 Normal;  // tangent space normal, if written
    fixed3 Emission;
    half Specular;  // specular power in 0..1 range
    fixed Gloss;    // specular intensity
    fixed Alpha;    // alpha for transparencies
};
```

在 Unity5中，surface shaders 能使用基于物理的光照模式。内置Standard 和 StandardSpecular模式分别使用各自的输出结构体，如下：

```
struct SurfaceOutputStandard
{
    fixed3 Albedo;      // base (diffuse or specular) color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Metallic;      // 0=non-metal, 1=metal
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};

struct SurfaceOutputStandardSpecular
{
    fixed3 Albedo;      // diffuse color
    fixed3 Specular;    // specular color
    fixed3 Normal;      // tangent space normal, if written
    half3 Emission;
    half Smoothness;    // 0=rough, 1=smooth
    half Occlusion;     // occlusion (default 1)
    fixed Alpha;        // alpha for transparencies
};
```

## 例子
参考 [Surface Shader Examples](SurfaceShaderExamples/README.md), [Surface Shader Custom Lighting Examples](SurfaceShaderCustomLightingExamples/README.md) and [Surface Shader Tessellation](SurfaceShaderTessellation/README.md) 页面.


## Surface Shader 编译指令
Surface shader和其他shader一样，放在 CGPROGRAM..ENDCG 代码块之间。他们的不同点是：
* 必须放在 [SubShader](../ShaderLabSyntax/ShaderLabSubShader/README.md) 块，而不是 [Pass](../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/README.md)
* 使用 ` #pragma surface ...` 指令来指明它是 surface shader。

` #pragma surface ...` 指令说明：  
> #pragma surface surfaceFunction lightModel [optionalparams]

## 必须参数
* surfaceFunction - 包含了 surface shader 代码的 CG 函数。该函数的结构为 `void surf (Input IN, inout SurfaceOutput o)`, Input 时自己定义的结构体。Input 应该包含 surface function 中的所有纹理坐标和额外的变量。
* lightModel - 使用的光照模型。内置的是基于物理的Standard 和 StandardSpecular，还有简单的非基于物理的Lambert (diffuse) 和 BlinnPhong (specular)。参考 [Custom Lighting Models ](CustomLightingmodelsinSurfaceShaders/README.md)页面学习如何实现自己的光照模型。
  * Standard 光照模型使用 SurfaceOutputStandard 输出结构体，在unity中为 Standard (metallic workflow) shader
  * StandardSpecular 光照模型使用 SurfaceOutputStandardSpecular  输出结构体，在unity中为 Standard (specular setup) shader。
  * Lambert 和 BlinnPhong 光照模型不是基于物理的，但是使用这些的shader在低端机上渲染效率更快。

## 可选参数
* **Transparency 和 alpha testing**用于控制alpha 和 alphatest指令。透明度通常有两种：传统的alpha混合（用于淡出物体）和 更符合物理规律的“预乘混合”（允许半透膜的表面保留适当的高光反射）。启用半透膜使生成的 surface shader 代码包含混合命名;然而启用alpha cutout在生成的代码中根据给定的变量执行偏远舍弃。
  * **alpha 或者 alpha:auto**：对于简单的光照函数将选择淡出透明度 (如 alpha:fade ) ，在基于物理光照的函数中奖预乘透明度（如 alpha:premul）
  * **alpha:blend**：启用 alpha blending
  * **alpha:fade**：启用传统的淡出透明度
  * **alpha:premul**：启用预乘透明度
  * **alphatest:VariableName**：启用透明度裁剪。裁剪值时 VariableName 属性的值。很有可能使用 addshadow 指令生成适当的阴影投射Pass。
  * **keepalpha**：默认情况下，不论输出结构体中的Alpha值为多少，光照函数返回的值，不透明的surface shaders的透明通道都写入1.使用改选项允许保留光照函数的透明通道值，即使是不透明的surface shaders。
  * **decal:add**：附加贴花shader（例如  terrain AddPass）。这意味着在其他物体的表面上，并且使用附加混合。参考[Surface Shader Examples](SurfaceShaderExamples/README.md)
  * **decal:blend**：半透明贴花shader。这意味着在其他物体的表面上，并且使用透明度混合。参考[Surface Shader Examples](SurfaceShaderExamples/README.md)

* **自定义修改函数**用于更改或者计算输入顶点数据，或者计算最终的片元颜色。
  * **vertex:VertexFunction**：自定义顶点修改函数。该函数在生成的vertex shader的开始处调用，并修改盒计算每个顶点数据。参考[Surface Shader Examples](SurfaceShaderExamples/README.md)
  * **finalcolor:ColorFunction**：自定义最终颜色修改函数。参考[Surface Shader Examples](SurfaceShaderExamples/README.md)
  * **finalgbuffer:ColorFunction**：自定义延迟路径用于修改gbuffer内容。
  * **finalprepass:ColorFunction**：自定义预处理基本路径。

* **阴影和曲面细分**附加质量用于控制阴影和曲面细分处理。
  * **addshadow**：生成阴影投射Pass。通常和自定义顶点修改一起使用，以便于阴影投射能获得任意过程中的顶点运动。通常shaders不需要任何特殊的阴影处理，因为能从fallback中获得阴影投射。
  * **fullforwardshadows**：支持所有的[Forward rendering path](../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)光照阴影类型。默认情况下shaders只支持向前渲染中一个平行光源的阴影（用于节约shader内部的变量数量）。如果在向前渲染需要点光源或者束光源阴影，使用该指令。
  * **tessellate:TessFunction**：使用DX11 GPU曲面细分，该方法计算曲面细分系数。参考[Surface Shader Tessellation](SurfaceShaderTessellation/README.md)

* **代码生成选项**默认情况下生成的surface shader代码尝试处理所有可能的光照/阴影/光照贴图情况。然而有些情况并不需要他们全部，并且通过调整生成的代码忽略他们。这样能生成更小的shader更快被加载。
  * **exclude_path:deferred, exclude_path:forward, exclude_path:prepass**：不生成指定的渲染路径的passe ([Deferred Shading](../../RenderingPipelineDetails/DeferredShadingRenderingPath/README.md), [Forward](../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md) 和 [Legacy Deferred respectively](../../RenderingPipelineDetails/LegacyDeferredLightingRenderingPath/README.md))。
  * **noshadow**：禁用当前shader中的所有阴影接收。
  * **noambient**：不引用任何环境光和光照探针。
  * **novertexlights**：在向前渲染中不应用所有的光照探针和逐顶点光源。
  * **nolightmap**：禁用光照贴图支持。
  * **nodynlightmap**：禁用shader中运行时动态全局光照支持。
  * **nodirlightmap**：禁用shader中方向光照贴图支持。
  * **nofog**：禁用所有的内置雾化支持。
  * **nometa**：不生成“meta” pass（用于光照贴图和动态全局光照提取表面信息）。
  * **noforwardadd**：禁用[向前渲染路径](../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)的附加Pass。这使得shader支持一个完整的平行光源，其他的光源计算使用逐顶点/SH。也能减小shader。
  * **nolppv**：禁用shader中的光照探针代理支持。
* **其他选项**
  * **softvegetation**：是surface shader只有在Soft Vegetation打开的情况下才渲染。
  * **interpolateview**：在vertex shader中计算视线方向并插值，而不是在pixel shader中计算它。这样会使pixel shader更快，但是会使用更多的纹理插值器。
  * **halfasview**：将半方向向量传递给光照函数，而不是视线方向。逐顶点将计算并单位化半方向向量。这样更快，但是不完全正确。
  * **approxview**：Unity 5.0中移除。interpolateview 代替。
  * **dualforward**：在[向前渲染路径](../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)中使用[dual lightmaps](../../../Lighting/GlobalIllumination/GraphicsOverview)

查看使用以上不同选项间的不同，使用 Shader Inspector 中的 “Show Generated Code” 按键非常有帮助。


## Surface Shader input structure
输入结构体Input包含该shader所需要的所有的纹理坐标。纹理坐标必须命名为 "uv"后面跟随纹理名（或者以 “uv2”开始使用第二个纹理坐标集）。
其他能放在输入结构体的值：
* **float3 viewDir**：包含视线方向，用于计算视差效果，边缘光等。
* **float4（COLOR 语义）**：包含每个顶点的颜色插值。
* **float4 screenPos**：包含屏幕坐标用于反射或者屏幕空间效果。请注意这不适用于 GrabPass，需要使用 ComputeGrabScreenPos 函数计算自定义的。
* **float3 worldPos**：包含世界坐标。
* **float3 worldRefl**：如果表面着色器不写入o.Normal，则包含世界反射向量。例如，请参见“反射-漫反射”着色器。
* **float3 worldNormal**：如果曲面着色器不写入o.Normal，则包含世界法线向量。
* **float3 worldRefl; INTERNAL_DATA**：如果表面着色器写入o.Normal，则包含世界反射向量。要获取基于每像素法线贴图的反射矢量，请使用WorldReflectionVector (IN, o.Normal)。例如，请参见“反射凹凸”着色器。
* **float3 worldNormal; INTERNAL_DATA**：如果表面着色器写入o.Normal，则包含世界法线向量。要基于每个像素的法线贴图获取法线向量，请使用WorldNormalVector (IN, o.Normal)。

## Surface Shaders and DirectX 11 HLSL syntax
当前，表面着色器编译管道的某些部分不了解DirectX 11特定的HLSL语法，因此，如果您正在使用诸如StructuredBuffers，RWTextures和其他非DX9语法之类的HLSL功能，则必须将其包装到仅DX11的预处理器宏中。

有关详细信息，请参见平台特定差异和着色语言页面。