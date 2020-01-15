## Shader Compilation Target Levels
当写[Surface Shaders](../../WritingSurfaceShaders/README.md)或者常规[Shader Programs](../README.md)。HLSL 源码能被编译到不同的  “shader models”。要允许使用更现代的GPI功能，必须使用更高的着色器编译目标。
**注意**：使用较高的着色器编译目标可能会阻止着色器在较旧的GPU或平台上运行。
通过使用`#pragma target name`指令或更具体的`#pragma require feature …`指令指示编译目标。例如：
```
#pragma target 3.5
#pragma require integers 2darray instancing
```

## Default compilation target
默认情况下，Unity编译shader到几乎最低支持目标(2.5)；在DirectX shader模式2.0到3.0之间。一些其他编译质量使shader自动被编译到更高的目标：
* geometry shader (#pragma geometry) 设置编译目标为 `4.0`
* tessellation shaders (#pragma hull or #pragma domain)设置编译目标为 `4.6`

任意没有为  geometry, hull 或者 domain shaders 通过`#pragma`明确指定函数入口的shader会降低内部着色器的能力要求。这允许具有更广泛的运行时和特性差异的非dx11目标与现有着色器内容更加兼容。
例如，Unity支持 Metal graphics 上的镶嵌着色，但是Metal不支持几何着色。只要不使用几何着色器，使用#pragma target 5.0仍然有效。

## Supported ‘#pragma target’ names
以下是受支持的着色器模型的列表，其中包含大致增加的功能集（在某些情况下，对平台/ GPU的要求更高）：

### #pragma target 2.0
* 适用于Unity支持的所有平台。DX9着色器模型2.0。
* 数量有限的算术和纹理指令；8个插值器；没有顶点纹理采样；片段着色器中没有倒数。没有明确的LOD纹理采样。

### #pragma target 2.5 (default)
* 几乎与3.0目标相同（请参见下文），除了仍然只有8个插值器，并且没有明确的LOD纹理采样。
* 在Windows Phone上编译为DX11功能级别9.3。

### #pragma target 3.0
* DX9着色器模型3.0：倒数指令，纹理LOD采样，10个插值器，允许使用更多的数学/纹理指令。
* DX11功能级别9.x GPU（例如，大多数Windows Phone设备）不支持。
* 某些OpenGL ES 2.0设备可能不完全支持它，具体取决于当前的驱动程序扩展和使用的功能。

### #pragma target 3.5 (or es3.0)
* OpenGL ES 3.0功能（在D3D平台上为DX10 SM4.0，仅没有几何着色器）。
* DX11 9.x（WinPhone），OpenGL ES 2.0不支持。
* 在DX11 +，OpenGL 3.2 +，OpenGL ES 3 +，Metal，Vulkan，PS4 / XB1控制台上受支持。
* 着色器中的整数操作，纹理数组等。

### #pragma target 4.0
* DX11着色器模型4.0。
* DX11 9.x（WinPhone），OpenGL ES 2.0 / 3.0 / 3.1，Metal不支持。
* 在DX11 +，OpenGL 3.2 +，OpenGL ES 3.1 + AEP，Vulkan，PS4 / XB1控制台上受支持。
* 具有几何着色器以及es3.0目标所具有的一切。

### #pragma target 4.5 (or es3.1)
* OpenGL ES 3.1功能（D3D平台上为DX11 SM5.0，仅不具有细分着色器）。
* 在SM5.0之前的DX11、4.3之前的OpenGL（即Mac），OpenGL ES 2.0 / 3.0上不支持DX11。
* 在DX11 + SM5.0，OpenGL 4.3 +，OpenGL ES 3.1，Metal，Vulkan，PS4 / XB1控制台上受支持。
* 具有计算着色器，随机访问纹理写入，原子等。没有几何或镶嵌着色器。

### #pragma target 4.6 (or gl4.1)
* OpenGL 4.1功能（在D3D平台上为DX11 SM5.0，仅不具有计算着色器）。这基本上是Mac支持的最高OpenGL级别。
* 在SM5.0之前的DX11、4.1之前的OpenGL，Op​​enGL ES 2.0 / 3.0 / 3.1，Metal上不支持DX11。
* 在DX11 + SM5.0，OpenGL 4.1 +，OpenGL ES 3.1 + AEP，Vulkan，Metal（无几何图形），PS4 / XB1控制台上受支持。

### #pragma target 5.0
* DX11着色器模型5.0。
* 在SM5.0之前的DX11、4.3之前的OpenGL（例如Mac），OpenGL ES 2.0 / 3.0 / 3.1，Metal不支持DX11。
* 在DX11 + SM5.0，OpenGL 4.3 +，OpenGL ES 3.1 + AEP，Vulkan，Metal（无几何图形），PS4 / XB1控制台上受支持。

请注意，所有类似OpenGL的平台（包括移动平台）都被视为“能够使用着色器模型3.0”。WP8 / WinRT平台（DX11功能级别9.x）被视为仅支持着色器模型2.5。

## Supported ‘#pragma require’ names
#pragma require指令支持的功能名称列表：

* interpolators10：至少有10个顶点到片段的插值器(“varyings”) 。
* interpolators15：至少有15个顶点到片段的插值器(“varyings”) 。
* interpolators32：至少有32个顶点到片段的插值器(“varyings”) 。
* mrt4：多个渲染目标，至少4个。
* mrt8：多个渲染目标，至少为8。
* derivatives：像素着色器派生指令（ddx / ddy）。
* samplelod：显式纹理LOD采样（tex2Dlod / SampleLevel）。
* fragcoord：在像素着色器中输入像素位置（屏幕上的XY，剪辑空间中的ZW深度）。
* integers：整数是实际的数据类型，包括位/移位操作。
* 2darray：2D纹理数组（Texture2DArray）。
* cubearray：立方体数组（CubemapArray）。
* instancing：SV_InstanceID输入系统值。
* geometry：DX10几何着色器。
* compute：计算着色器，结构化缓冲区，原子操作。
* randomwrite：“随机写入”（UAV）纹理。
* tesshw：GPU支持硬件细分，但不一定支持细分着色器阶段（例如，金属支持细分，但不支持着色器阶段）。
* tessellation：细分船体/域着色器阶段。
* msaatex：能够访问多采样纹理（HLSL中的Texture2DMS）。
* sparsetex：具有居留信息的稀疏纹理（D3D术语中的“ Tier2”支持； CheckAccessFullyMapped HLSL函数）。请注意，当前仅在DX11 / 12上实现。
* framebufferfetch：帧缓冲区提取–能够读取像素着色器中的输入像素颜色。


广泛的#pragma target指令是上述要求的简写，它们对应于：
* 2.5：衍生品
* 3.0：2.5 +内插器10 + samplelod + fragcoord
* 3.5：3.0 +内插器15 + mrt4 +整数+ 2darray +实例化
* 4.0：3.5 +几何
* 5.0：4.0 +计算+ randomwrite + tesshw +细分
* 4.5：3.5 +计算+ randomwrite
* 4.6：4.0 + cubearray + tesshw +细分

请注意，在Direct3D术语中，着色器模型4.0也暗指“ mrt8”。着色器模型5.0包含“ interpolators32”和“ cubearray”。但是，不能保证这些功能可在许多移动平台上使用。因此，为了与现有着色器向后兼容，编写#pragma target 4.0并不需要自动支持8个MRT。编写#pragma target 5.0不需要32个插值器也不需要cubemap数组。

## See Also
[Writing Shader Programs](../README.md).
[Surface Shaders](../../WritingSurfaceShaders/README.md).