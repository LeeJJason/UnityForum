## Writing vertex and fragment shaders
ShaderLab shader 不仅仅是包含“hardware shaders”。还做了很多事情。描述了Material Inspector中显示的属性，包含对不同显卡支持的多个shader，配置固定功能的硬件状态等。该实际的可编程shader-比如vertex 和 fragment 程序 - 仅仅是 ShaderLab 的 Shader 概念的一部分。k看一下[shader tutorial ](https://docs.unity3d.com/Manual/ShaderTut2.html)了解基本介绍。这里我们将调用底层硬件着色器着色器程序。

如果要编写与光照交互的着色器，请查看[Surface Shaders](../WritingSurfaceShaders/README.md)文档。对于某些示例，请查看[Vertex and Fragment Shader Examples](../VertexAndFragmentShaderExamples/README.md)。该页面的其余部分假定着色器不与Unity灯光交互（例如，特殊效果，后处理效果等）。

Shader 程序使用 [HLSL language](../ShadingLanguageUsedInUnity/README.md)，在shader文本中嵌入[Pass](https://docs.unity3d.com/Manual/SL-Pass.html)命令内部的某些位置。它们通常如下所示：
```
  Pass {
      // ... the usual pass state setup ...
      
      CGPROGRAM
      // compilation directives for this snippet, e.g.:
      #pragma vertex vert
      #pragma fragment frag
      
      // the Cg/HLSL code itself
      
      ENDCG
      // ... the rest of pass setup ...
  }
```

## HLSL snippets
HLSL 程序片段写在关键字 **CGPROGRAM** 和 **ENDCG** 之间，或者  **HLSLPROGRAM** 和 **ENDHLSL** 之间。后一种形式并不会自动包括HLSLSupport和UnityShaderVariables的[built-in header files](Built-inShaderIncludeFiles/README.md)。

在代码段的开头，可以指定编译指令为#pragma语句。指令指定编译的shader函数：
* **#pragma vertex** *name* - 将name函数编译为 vertex shader。
* **#pragma fragment** *name* - 将name函数编译为 fragment shader。
* **#pragma geometry** *name* - 将函数名称编译为DX10 geometry shader。拥有此选项会自动打开#pragma target 4.0，详情如下。
* **#pragma hull** *name* - 将函数名称编译为DX11 hull shader。拥有此选项会自动打开#pragma target 5.0，详情如下。
* **#pragma domain** *name* - 编译功能名称作为DX11 domain shader。拥有此选项会自动打开#pragma target 5.0，详情如下。

其他编译指令：
* **#pragma target** *name* - 要编译到的着色器目标。有关详细信息，请参见[ Shader Compilation Targets](ShaderCompilationTargetLevels/README.md)。
* **#pragma require** *feature …* - 细粒度控制shader需要GPU的的特性，有关详细信息，请参见[ Shader Compilation Targets](ShaderCompilationTargetLevels/README.md)。
* **#pragma only_renderers** *space separated names * - 仅编译指定渲染器的shader。默认情况下，为所有渲染器编译着色器。有关详细信息，请参见下面的渲染器。
* **#pragma exclude_renderers** *space separated names * - 不编译指定渲染器的shader。默认情况下，为所有渲染器编译着色器。有关详细信息，请参见下面的渲染器。
* **#pragma multi_compile…** - 处理[multiple shader variants](MakingMultipleShaderProgramVariants/README.md)。**multi_compile shader**未使用的变量包含在游戏版本中。
* **#pragma multi_compile_local…** - 与**multi_compile**相似，但枚举关键字是局部的。有关更多信息，请参见[Making multiple shader program variants: Keyword limits ](MakingMultipleShaderProgramVariants/README.md)。
* **#pragma shader_feature… - 处理[multiple shader variants](MakingMultipleShaderProgramVariants/README.md)。**shader_feature shader**未使用变体不包括在游戏版本中。
* **#pragma shader_feature_local…** - 与**shader_feature**相似，但枚举关键字是局部的。有关更多信息，请参见[Making multiple shader program variants: Keyword limits ](MakingMultipleShaderProgramVariants/README.md)。
* **#pragma enable_d3d11_debug_symbols** - 生成针对DirectX 11编译的着色器的调试信息，这将允许您通过Visual Studio 2012（或更高版本）图形调试器调试shader。
* **#pragma hardware_tier_variants** *renderer name* - 为每个能运行当前渲染器的硬件层生成不同的[multiple shader variants](MakingMultipleShaderProgramVariants/README.md)。有关详细信息，请参见下面的渲染器。
* **#pragma hlslcc_bytecode_disassembly** - 将反汇编的HLSLcc字节码嵌入翻译的着色器中。
* **#pragma disable_fastmath** - 启用精确的IEEE 754规则，主要涉及NaN处理（当前仅影响Metal平台）。
* **#pragma glsl_es2** - 在GLSL着色器中进行设置时，即使着色器目标是OpenGL ES 3，也将生成GLSL ES 1.0（OpenGL ES 2.0）。
* **#pragma editor_sync_compilation** - 强制同步编译（仅影响编辑器）。

每个代码片段都必须包含至少一个 vertex program 和 fragment program。因此 **#pragma vertex** 和 **#pragma fragment** 指令是必须的。
在Unity 5.0以后，无效的编译指令可以安全地删除： **#pragma glsl**, **#pragma glsl_no_auto_normalization**, **#pragma profileoption**, **#pragma fragmentoption**。
Unity仅在着色器文件中支持#pragma指令，而在includes中不支持。

## Rendering platforms
Unity支持多种渲染API（例如Direct3D 11和OpenGL），默认情况下，所有着色器程序都编译到所有受支持的渲染器中。您可以使用 **#pragma only_renderers** 或 **#pragma exclude_renderers** 指令指示要编译的渲染器。当你明确使用一些shader语言特性，在一些平台上不支持时尤其有效。支持的 renderer name 如下：
* d3d11 - Direct3D 11/12
* glcore - OpenGL 3.x/4.x
* gles - OpenGL ES 2.0
* gles3 - OpenGL ES 3.x
* metal - iOS/Mac Metal
* vulkan - Vulkan
* d3d11_9x - Direct3D 11 9.x feature level, as commonly used on WSA platforms
* xboxone - Xbox One
* ps4 - PlayStation 4
* n3ds - Nintendo 3DS
* wiiu - Nintendo Wii U

## See Also
* [Vertex and fragment shader examples](VertexAndFragmentShaderExamples/README.md)
* [Shader semantics](ShaderSemantics/README.md)
* [Accessing shader properties in Cg/HLSL](AccessingShaderPropertiesInCgHLSL/README.md)
* [Providing vertex data to vertex programs](ProvidingVertexDataToVertexPrograms/README.md)
* [Built-in shader include files](Built-inShaderIncludeFiles/README.md)
* [Predefined Shader preprocessor macros](PredefinedShaderPreprocessorMacros/README.md)
* [Built-in shader helper functions](Built-inShaderHelperFunctions/README.md)
* [Built-in shader variables](Built-inShaderVariables/README.md)
* [Making multiple shader program variants](MakingMultipleShaderProgramVariants/README.md)
* [GLSL Shader programs](GLSLShaderPrograms/README.md)
* [Shading Language used in Unity](ShadingLanguageUsedInUnity/README.md)
* [Shader Compilation Target Levels](ShaderCompilationTargetLevels/README.md)
* [Shader data types and precision](ShaderDataTypesAndPrecision/README.md)
* [Using sampler states](UsingSamplerStates/README.md)