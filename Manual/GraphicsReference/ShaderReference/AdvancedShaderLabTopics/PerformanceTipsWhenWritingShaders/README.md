## Performance tips when writing shaders
### Only compute what you need
你的shader处理越多的计算和处理，它就会越影响游戏的性能。比如，每个材质都支持控制颜色会使shader更灵活，但是如果你总是把该颜色设置为白色，那么对于每个顶点或像素在呈现在屏幕上时就会执行无用的计算。
计算的频率也会影响游戏的性能。通常渲染的像素(随后渲染的像素也比顶点着色器的执行要多)比顶点(顶点着色器的执行)要多很多，顶点也比被渲染的对象要多。在可能的情况下，将计算从像素着色器代码移到顶点着色器中编写代码，或者将它们完全移出着色器，并在脚本中设置值。

### Optimized Surface Shaders
[Surface Shaders](../../WritingSurfaceShaders/README.md)用于写光照交互的shader很方便。然而，它们的默认选项被调优以涵盖大量的一般情况。调整这些特定的情况下，使着色器运行更快或至少更小:
* `approxview` 指令用于使用视图方向的着色器(如高光)，使得每个顶点的视图方向都标准化，而不是每个像素。这是近似的，但通常已经足够好了。
* `halfasview` 用于 Specular 类型shader会更快，每个顶点计算并归一化half-vector(照明方向和视图向量的中间部分)，照明函数接收半向量作为参数而不是视图向量。[lighting function](../../WritingSurfaceShaders/SurfaceShaderLightingExamples/README.md)
* `noforwardadd` 在**Forward rendering**中只完全支持一个平行光。剩余的光源仍然可以作为per-vertex lights 或者 spherical harmonics产生效果。这对使shader更小并且总是在一个pass中渲染很有帮助，即使有多个光源表现。
* `noambient` 关闭一个shader的环境光源和spherical harmonics光源。这可以使性能稍微快一些。

### Precision of computations
当用 Cg/HLSL 写shader时，这儿有三种基本数字类型： `float`, `half` 和 `fixed` (参考 [Data Types and Precision](../../WritingVertexAndFragmentShaders/ShaderDataTypesAndPrecision/README.md))。
为了获得良好的性能，请始终使用尽可能低的精度。这在移动平台上尤其重要比如iOS和Android等。好的经验法则是:
* 世界坐标和纹理坐标，使用`float`精度。
* 对于其他所有东西(矢量，HDR颜色，等等)，开始的时候使用`half`精度。只有在必要时才增加。
* 对纹理非常简单操作的数据，使用`fixed`精度。

实际上，具体应该使用哪种数字类型取决于平台和GPU。一般来说:
* 所有现代的桌面gpu总是以完全`float`精度来计算所有东西，所以`float/half/fixed`的结果在底层是完全相同的。这可能会使测试变得困难，因为很难看到`half/fixed`精度是否足够，所以总是在目标设备上测试着色器以获得准确的结果。
* 移动gpu有实际的`half`精度支持。这通常更快，而且计算时消耗的能量更少。
* `fixed`精度通常只对较老的移动gpu有用。大多数现代gpu(那些可以运行OpenGL ES 3或Metal的gpu)内部处理`fixed`和`half`精度完全相同。

有关详细信息，请参见[Data Types and Precision](../../WritingVertexAndFragmentShaders/ShaderDataTypesAndPrecision/README.md)。

### Alpha Testing
固定函数[AlphaTest](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/ShaderLabLegacyAlphaTesting/README.md) - 或其可编程的等效物`clip()` - 在不同的平台上具有不同的性能特征:

* 通常，在大多数平台上使用它来删除完全透明的像素时，您会获得一个小优势。
* 然而，在iOS和一些Android设备上的PowerVR gpu上，alpha测试是资源密集型的。不要尝试在这些平台上使用它来进行性能优化，因为它会导致游戏运行速度比平时慢。

### Color Mask
在某些平台上(主要是iOS和Android设备上的移动gpu)，使用[ColorMask](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/README.md)去掉某些通道(如ColorMask RGB)可能会占用大量资源，因此只有在真正需要时才使用它。