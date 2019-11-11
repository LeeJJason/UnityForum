## Predefined Shader preprocessor macros
在编译[Shader程序](../README.md)时，Unity定义了几个预处理器宏。

## Target platform
|Macro:	|Target platform:|
|:------|:---------------|
|SHADER_API_D3D11|	Direct3D 11|
|SHADER_API_GLCORE|	Desktop OpenGL “core” (GL 3/4)|
|SHADER_API_GLES|	OpenGL ES 2.0|
|SHADER_API_GLES3|	OpenGL ES 3.0/3.1|
|SHADER_API_METAL|	iOS/Mac Metal|
|SHADER_API_VULKAN|	Vulkan|
|SHADER_API_D3D11_9X|	Direct3D 11 “feature level 9.x” target for Universal Windows Platform|
|SHADER_API_PS4|	PlayStation 4. SHADER_API_PSSL is also defined.|
|SHADER_API_XBOXONE|	Xbox One|
`SHADER_API_MOBILE` 为所有通用移动平台（GLES，GLES3，METAL）定义。
此外，`SHADER_TARGET_GLSL`在目标着色语言为GLSL时定义（对于OpenGL / GLES平台始终如此）。

## Shader target model
`SHADER_TARGET`定义为与Shader目标编译模型匹配的数值（即，匹配#pragma target指令）。例如，SHADER_TARGET是30编译为着色器模型3.0时。您可以在Shader代码中使用它来进行条件检查。例如：
```
#if SHADER_TARGET < 30
    // less than Shader model 3.0:
    // very limited Shader capabilities, do some approximation
#else
    // decent capabilities, do a better thing
#endif
```

## Unity version
`UNITY_VERSION`包含Unity版本的数值。例如，`UNITY_VERSION`是`501`用于统一5.0.1。如果需要编写使用其他内置Shader功能的Shader，则可以将其用于版本比较。例如，#if UNITY_VERSION >= 500预处理程序检查仅在5.0.0或更高版本上通过。

## Shader stage being compiled
预处理宏`SHADER_STAGE_VERTEX`，`SHADER_STAGE_FRAGMENT`，`SHADER_STAGE_DOMAIN`，`SHADER_STAGE_HULL`，`SHADER_STAGE_GEOMETRY`，`SHADER_STAGE_COMPUTE`正在编制每个着色阶段时定义。通常，在pixel Shaders 和 compute Shaders共享Shader代码时，它们很有用，以处理某些情况下必须做一些稍有不同的情况。

## Platform difference helpers
不建议直接使用这些平台宏，因为它们并不总是有助于您的代码过时。例如，如果要编写检查D3D11的着色器，则可能需要确保将来将检查扩展到包括Vulkan。相反，Unity定义了几个辅助宏（在中[HLSLSupport.cginc](../Built-inShaderIncludeFiles/README.md)）：
|Macro:	|Target platform:|
|:------|:---------------|
|UNITY_BRANCH|在条件语句之前添加此代码，以告知编译器应将此代码编译为实际分支。HLSL平台上时扩展到[branch]。|
|UNITY_FLATTEN|	在条件语句之前添加此代码，以告知编译器应将其展平以避免实际的分支指令。HLSL平台上时扩展到[flatten]。|
|UNITY_NO_SCREENSPACE_SHADOWS|	在不使用级联屏幕空间阴影贴图的平台（移动平台）上定义。|
|UNITY_NO_LINEAR_COLORSPACE|	在不支持线性色彩空间的平台（移动平台）上定义。|
|UNITY_NO_RGBM|	定义在没有使用RGBM压缩光照贴图的平台上（移动平台）。|
|UNITY_NO_DXT5nm|	在不使用DXT5nm法线贴图压缩的平台（移动平台）上定义。|
|UNITY_FRAMEBUFFER_FETCH_AVAILABLE|	在可以使用“framebuffer color fetch”功能的平台上定义（通常为iOS平台-OpenGL ES 2.0、3.0和Metal）。|
|UNITY_USE_RGBA_FOR_POINT_SHADOWS|	在点光源阴影贴图使用RGBA纹理编码深度的平台上定义（其他平台使用单通道浮点纹理）。|
|UNITY_ATTEN_CHANNEL|	定义光衰减Texture的哪个通道包含数据；用于每个像素的照明代码。定义为“ r”或“ a”。|
|UNITY_HALF_TEXEL_OFFSET|	定义在需要将半像素偏移调整以将像素映射到像素的平台上（例如Direct3D 9）。|
|UNITY_UV_STARTS_AT_TOP|	始终使用值1或0定义。在纹理“顶部”的纹理V坐标为0的平台上，值为1。类似Direct3D的平台的值为1；类似OpenGL的平台使用值0。|
|UNITY_MIGHT_NOT_HAVE_DEPTH_Texture|	定义平台是否可以通过手动渲染来模拟阴影贴图或深度纹理深入纹理。|
|UNITY_PROJ_COORD(a)|	给定一个4分量向量，它返回适合投影的Texture读取的Texture坐标。在大多数平台上，这直接返回给定值。|
|UNITY_NEAR_CLIP_VALUE|	定义为“接近剪切平面”的值。类似Direct3D的平台使用0.0，而使用OpenGL的平台使用–1.0。|
|UNITY_VPOS_TYPE|	定义float2D3D9或float4其他位置上像素位置输入（VPOS）所需的数据类型。|
|UNITY_CAN_COMPILE_TESSELLATION|	在Shader编译器“理解”细分Shader HLSL语法（当前仅D3D11）时定义。|
|UNITY_INITIALIZE_OUTPUT(type,name)|	将给定类型的变量名称初始化为零。|
|UNITY_COMPILER_HLSL，UNITY_COMPILER_HLSL2GLSL，UNITY_COMPILER_CG|	指示分别使用哪个Shader编译器来编译Shaders：Microsoft的HLSL，HLSL到GLSL转换器以及NVIDIA的Cg。有关更多详细信息，请参见有关着色语言的文档。如果您遇到了非常具体的Shader语法处理编译器之间的差异，并希望为每个编译器编写不同的代码，请使用此方法。
|UNITY_REVERSED_Z|使用反向Z缓冲区在plaftorms上定义。存储的Z值在1..0范围内，而不是0..1。|

## Shadow mapping macros
取决于平台，声明和采样阴影图可能会非常不同。Unity有几个宏可以帮助您：
|Macro:	|Target platform:|
|:------|:---------------|
|UNITY_DECLARE_SHADOWMAP(tex)|	声明一个阴影贴图纹理变量，名称为“ tex”。|
|UNITY_SAMPLE_SHADOW(tex,uv)|	在给定的“ uv”坐标处采样阴影贴图纹理“ tex”（XY分量是“纹理”位置，Z分量是要与之进行比较的深度）。返回带有阴影项在0..1范围内的单个float值。|
|UNITY_SAMPLE_SHADOW_PROJ(tex,uv)|	与上述类似，但会读取投影阴影贴图。“ uv”是float4，所有其他组件都用.w除以进行查找。|
**注意**：并非所有图形卡都支持阴影贴图。使用[SystemInfo.SupportsRenderTextureFormat](https://docs.unity3d.com/ScriptReference/SystemInfo.SupportsRenderTextureFormat.html)来检查支持。

## Constant buffer macros
Direct3D 11将所有的着色器变量分组到“常量缓冲”中。Unity的大多数内置变量已经被分组了，但是对于你自己的着色器中的变量来说，根据预期的更新频率，把它们放到独立的常量缓冲区中可能是更优的选择。
使用`CBUFFER_START(name)`和`CBUFFER_END`宏：
```
CBUFFER_START(MyRarelyUpdatedVariables)
    float4 _SomeGlobalValue;
CBUFFER_END
```

## Texture/Sampler declaration macros
通常，您将texture2D在Shader代码中使用以声明Texture和Sampler对。但是，在某些平台（例如DX11）上，纹理和采样器是单独的对象。并且很大可能的采样器数量非常有限。Unity有一些宏来声明没有采样器的纹理，并使用另一个纹理中的采样器对纹理进行采样。如果最终遇到了Sampler限制，并且知道多个纹理实际上可以共享一个Sampler（Samplers定义Texture过滤和包装模式），请使用此选项。
|Macro:	|Target platform:|
|:------|:---------------|
|UNITY_DECLARE_TEX2D(name)|	声明一个Texture和Sampler对。|
|UNITY_DECLARE_TEX2D_NOSAMPLER(name)|	声明没有采样器的纹理。|
|UNITY_DECLARE_TEX2DARRAY(name)|	声明一个Texture Sampler数组变量。|
|UNITY_SAMPLE_TEX2D(name,uv)|	使用给定的纹理坐标从“纹理”和“采样器”对中采样。|
|UNITY_SAMPLE_TEX2D_SAMPLER( name,samplername,uv)|	使用另一个纹理（采样器名称）中的采样器，从纹理（名称）中采样。|
|UNITY_SAMPLE_TEX2DARRAY(name,uv)|	来自带有float3 UV的Texture数组的样本；坐标的z分量是数组元素索引。|
|UNITY_SAMPLE_TEX2DARRAY_LOD(name,uv,lod)|	具有显式mipmap级别的Texture数组的样本。|
有关更多信息，请参见有关[采样器状态](../UsingSamplerStates/README.md)的文档。

## Surface Shader pass indicators
[Surface Shaders](../../WritingSurfaceShaders/README.md)被编译时，它们为进行照明的各种PASS生成了大量代码。编译每个PASS时，将定义以下宏之一：
|Macro:	|Target platform:|
|:------|:---------------|
|UNITY_PASS_FORWARDBASE|	正向渲染基本pass（主方向光，光照贴图，SH）。|
|UNITY_PASS_FORWARDADD|	正向渲染附加pass（一个光源一个pass）。|
|UNITY_PASS_DEFERRED|	延迟阴影pass（提供g缓冲区）。|
|UNITY_PASS_SHADOWCASTER|	阴影投射器和深度纹理渲染pass。|
|UNITY_PASS_PREPASSBASE|	旧版延迟照明基础pass（渲染法线和镜面反射指数）。|
|UNITY_PASS_PREPASSFINAL|	旧版延迟照明最终pass（应用照明和纹理）。|

## Disable Auto-Upgrade
UNITY_SHADER_NO_UPGRADE 允许您禁止Unity自动升级或修改着色器文件。

## See also
* [Built-in Shader include files](../Built-inShaderIncludeFiles/README.md)
* [Built-in Shader variables](../Built-inShaderVariables/README.md)
* [Vertex and Fragment program examples](../VertexAndFragmentShaderExamples/README.md)