## Rendering Paths
Unity支持不同的渲染路径。你应该根据你的游戏内容和目标平台/硬件选择具体某一个。不同的渲染路径有不同的性能特性，这些特性主要影响灯光和阴影。技术细节见[render pipeline](../../GraphicsReference/ShaderReference/AdvancedShaderLabTopics/UnityRenderingPipeline/README.md)。

您的项目使用的呈现路径是在[Graphics window](https://docs.unity3d.com/Manual/class-GraphicsSettings.html)中选择的。另外，您可以为每个[相机](https://docs.unity3d.com/Manual/class-Camera.html)覆盖它。

如果显卡不能处理选定的渲染路径，Unity会自动使用一个较低保真度的渲染路径。例如，在一个不能处理延**Deferred Shading**的GPU上**Forward Rendering**就会被使用。

### Deferred Shading
*Deferred Shading*是最具照明和阴影保真度的渲染路径，如果你有很多实时灯光，它是最适合的。它需要一定程度的硬件支持。
有关更多细节，请参见[Deferred Shading page](../../../GraphicsReference/RenderingPipelineDetails/DeferredShadingRenderingPath/README.md)。

### Forward Rendering
*Forward*是传统的渲染路径。它支持所有典型的Unity图形功能(法线贴图，每像素灯，阴影等)。然而，在默认设置下，只有一小部分最亮的灯光是以逐像素照明模式呈现的。其余的灯光是在对象顶点或每个对象上计算的。
有关更多细节，请参见[Forward Rendering page](../../../GraphicsReference/RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)。

### Legacy Deferred
*Legacy Deferred (light prepass)*类似于延迟着色，只是使用了不同的技术和不同的权衡。它不支持Unity 5 基于物理的标准着色器。
有关更多细节，请参见[Deferred Lighting page](../../../GraphicsReference/RenderingPipelineDetails/LegacyDeferredLightingRenderingPath/README.md)。

### Legacy Vertex Lit
*Legacy Vertex Lit*是最低的照明保真度的渲染路径和不支持实时阴影。它是前向渲染路径的一个子集。
有关更多细节，请参见[Vertex Lit page](../../../GraphicsReference/RenderingPipelineDetails/VertexLitRenderingPathDetails/README.md)。

注意:使用**Orthographic projection**时不支持延迟渲染。如果相机的投影模式设置为**Orthographic**模式，则会覆盖这些值，并且相机将始终使用**Forward rendering**。

## Rendering Paths Comparison
|Features|Deferred|Forward|Legacy Deferred|Vertex Lit|
|:-------|:-------|:-------|:-------|:-------|:-------|
|Per-pixel lighting (normal maps, light cookies)|Yes|Yes|Yes|-|
|Realtime shadows|Yes|With caveats|Yes|-|
|Reflection Probes|Yes|Yes|-|-|
|Depth&Normals Buffers|Yes|Additional render passes|Yes|-|
|Soft Particles|Yes|-|Yes|-|
|Semitransparent objects|-|Yes|-|Yes|
|Anti-Aliasing|-|Yes|-|Yes|
|Light Culling Masks|Limited|Yes|Limited|Yes|
|Lighting Fidelity|All per-pixel|Some per-pixel|All per-pixel|All per-vertex|
|Performance||||
|Cost of a per-pixel Light|Number of pixels it illuminates|Number of pixels * Number of objects it illuminates|Number of pixels it illuminates|-|
Number of times objects are normally rendered|1|Number of per-pixel lights|2|1
|Overhead for simple scenes|High|None|Medium|None|
|**Platform Support**||||
|PC (Windows/Mac)|Shader Model 3.0+ & MRT|All|Shader Model 3.0+|All|
|Mobile (iOS/Android)|OpenGL ES 3.0 & MRT, Metal (on devices with A8 or later SoC)|All|OpenGL ES 2.0|All|
|Consoles|XB1, PS4|All|XB1, PS4, 360|-|