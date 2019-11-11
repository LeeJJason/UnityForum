## Shading Language used in Unity
在Unity中，[shader programs](../README.md)用[HLSL](https://en.wikipedia.org/wiki/High-Level_Shading_Language)的一个变种编写（也称为 [CG](https://en.wikipedia.org/wiki/Cg_%28programming_language%29), 但在大多数实际使用中两者是相同的）。
当前，为了在不同平台之间实现最大的可移植性，请以DX9样式的HLSL编写（例如，使用DX9样式sampler2D并tex2D用于纹理采样而不是DX10样式Texture2D，SamplerState以及tex.Sample）。

## Shader Compilers
内部，不同的shader编译器用于编译不同的[shader program](../README.md)：
* Windows和Microsoft平台（DX11，DX12和Xbox One）均使用Microsoft的HLSL编译器（当前为d3dcompiler_47）。
* OOpenGL Core中的OpenGL ES 3，OpenGL ES 2.0和Metal使用Microsoft的HLSL使用 [HLSLcc](https://github.com/Unity-Technologies/HLSLcc)将字节码转换为GLSL或Metal。
* OpenGL ES 2.0可以通过[hlsl2glslfork](https://github.com/aras-p/hlsl2glslfork)和[glsl优化器](https://github.com/aras-p/glsl-optimizer)使用源代码级别转换。通过添加启用#pragma prefer_hlsl2glsl gles
* 其他控制台平台使用其各自的编译器（例如PS4上的PSSL）。
* [Surface Shaders](../../WritingSurfaceShaders/README.md)，在代码生成分析步骤中使用Cg 2.2和 [MojoShader](https://icculus.org/mojoshader/)。

如果您确实需要确定哪个编译器被使用（以使用仅一个编译器支持的HLSL语法，或解决一个编译器错误），则可以使用[预定义的着色器宏](../PredefinedShaderPreprocessorMacros/README.md)。例如，`UNITY_COMPILER_HLSL`在使用HLSL编译器进行编译时设置（对于D3D或GLCore / GLES3 / GLES平台）；以及`UNITY_COMPILER_HLSL2GLSL`通过hlsl2glsl进行编译时。

## See Also
* [Shader Programs](../README.md)
* [Shader Preprocessor Macros](../PredefinedShaderPreprocessorMacros/README.md)
* [Platform Specific Rendering Differences]([../PredefinedShaderPreprocessorMacros/README.md](https://docs.unity3d.com/Manual/SL-ShadingLanguage.html))