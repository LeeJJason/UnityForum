## Shader data types and precision
Unity中标准的shader语言是[HLSL](../ShadingLanguageUsedInUnity/README.md),且支持一般的HLSL变量类型。然而Unity有一些对HLSL的额外类型，特别是为了在移动平台上提供更好的支持。

## Basic data types
shader中绝大多数的计算使用浮点数（和普通编程语言中的float一样，比如 C#）。存在几种浮点类型变体：float, half 和 fixed(以及 vector/matrix变体, 如 half3 和 float4x4)。这些类型在精度三不同（因此，性能或功耗也不同）。

* **High precision:** `float`
    最高精度的浮点数值；通常为32位（就像常规编程语言中的float一样）。
    完全浮点精度常用于世界空间位置，纹理坐标或涉及复杂函数（例如三角函数或幂/幂）的标量计算。
* **Medium precision:** `half`
    中精度浮点值；通常为16位（范围为–60000至+60000，精度为3位小数）。
    半精度对于短矢量，方向，对象空间位置，高动态范围颜色很有用。
* **Low precision:** `fixed`
    最低精度定点值。通常为11位，范围为–2.0至+2.0和1/256精度。
    固定精度对于常规颜色（通常存储在常规纹理中）和对其执行简单操作很有用。
* **Integer data types**
    整数（int数据类型）通常用作循环计数器或数组索引。为此，它们通常可以在各种平台上正常工作。
    根据平台的不同，GPU可能不支持整数类型。例如，Direct3D 9和OpenGL ES 2.0 GPU仅对浮点数据进行操作，并且可能使用相当复杂的浮点数学指令来模拟简单的整数表达式（涉及位或逻辑运算）。
    Direct3D 11，OpenGL ES 3，Metal和其他现代平台都对整数数据类型提供了适当的支持，因此按预期使用位移和位掩码可以正常工作。

## Composite vector/matrix types
HLSL具有从基本类型创建的内置向量和矩阵类型。例如，float3是具有.x，.y，.z分量的3D矢量，half4是具有.x，.y，.z，.w分量的中等精度4D矢量。或者，向量可以使用.r，.g，.b，.a分量对向量进行索引，这在处理颜色时很有用。
矩阵类型以类似的方式构建;例如，float4x4是一个4x4变换矩阵。注意，有些平台只支持方阵，最明显的是OpenGL ES 2.0。

## Texture/Sampler types
通常，您在HLSL代码中声明纹理，如下所示：
```
sampler2D _MainTex;
samplerCUBE _Cubemap;
```
对于移动平台，这些转换为“低精度采样器”，即，预期纹理中包含低精度数据。如果您知道您的纹理包含HDR颜色，则可能要使用半精度采样器：
```
sampler2D_half _MainTex;
samplerCUBE_half _Cubemap;
```
或者，如果您的纹理包含完整的浮动精度数据（例如，[depth texture](https://docs.unity3d.com/Manual/SL-DepthTextures.html)），请使用完整精度的采样器：
```
sampler2D_float _MainTex;
samplerCUBE_float _Cubemap;
```

## Precision, Hardware Support and Performance
float/half/fixed数据类型使用的一个复杂之处是PC gpu的精度总是很高。也就是说，对于所有的PC (Windows/Mac/Linux) gpu，在着色器中写入浮点型、半浮点型或固定数据类型并不重要。它们总是以32位浮点精度计算所有东西。
half 和 fixed 类型只有在目标平台是移动 GPUs时才有意义，这些类型主要是为了电源(有时是性能)约束而存在的。记住，你需要在手机上测试你的着色器，看看你是否遇到精度/数值问题。
即使在移动GPU上，不同的精度支持在GPU系列之间也有所不同。以下是每个移动GPU系列如何处理每种浮点类型的概述（由用于其的位数表示）：

|GPU Family|float|half|fixed|
|:---------|:----|:---|:----|
|PowerVR Series 6/7|32|16||
|PowerVR SGX 5xx|32|16|11|
|Qualcomm Adreno 4xx/3xx|32|16||
|Qualcomm Adreno 2xx|32 vertex 24 fragment|
|ARM Mali T6xx/7xx|32|16||
|ARM Mali 400/450|32 vertex 16 fragment|
|NVIDIA X1|32|16||
|NVIDIA K1|32|||
|NVIDIA Tegra 3/4|32|16||
实际上，大多数现代移动GPU仅支持32位数字（用于float类型）或16位数字（用于half和fixed类型）。一些较旧的GPU对顶点着色器精度有所不同和片段着色器计算。
使用较低的精度通常会更快，这可能是由于改善了GPU寄存器分配，或者是由于某些低精度数学运算具有特殊的“快速路径”执行单元。即使没有原始性能优势，使用较低的精度通常也会减少GPU的功耗，从而延长电池寿命。
一般的经验法则是，除了位置和纹理坐标外，其他所有内容都以半精度开始。仅在半精度对某些部分计算而言不够时才提高精度。

## Support for infinities, NaNs and other special floating point values
对特殊浮点值的支持可能会有所不同，具体取决于您所运行的（主要是移动的）GPU系列。
所有支持Direct3D 10的PC GPU都支持非常明确的IEEE 754浮点标准。这意味着浮点数的行为与在CPU上常规编程语言中的行为完全相同。
移动GPU的支持级别可能略有不同。在某些情况下，将零除以零可能会导致NaN（“非数字”）；在其他情况下，它可能会导致无穷大，零或任何其他未指定的值。确保在目标设备上测试着色器以检查它们是否受支持。

## External GPU Documentation
GPU供应商提供了有关其GPU性能和功能的深入指南。请参阅以下详细信息：
* [ARM Mali Guide for Unity Developers](https://developer.arm.com/products/software-development-tools/graphics-development-tools/mali-graphics-debugger/docs/100140/0303)
* [Qualcomm Adreno OpenGL ES Developer Guide](https://developer.qualcomm.com/software/adreno-gpu-sdk/tools)
* [PowerVR Architecture Guides](https://community.imgtec.com/developers/powervr/documentation/)

## See Also
* [Platform Specific Rendering Differences](https://docs.unity3d.com/Manual/SL-PlatformDifferences.html)
* [Shader Performance Tips](https://docs.unity3d.com/Manual/SL-ShaderPerformance.html)
* [Shading Language](../ShadingLanguageUsedInUnity/README.md)