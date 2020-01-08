## ShaderLab: Pass Tags
Passes 使用 Tags 来通知渲染引擎他们期望如何渲染。

### Syntax
```
Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```
指定`TagName1`的值为`Value1`, `TagName2`的值为`Value2`。你想要多少tags都可以。

### Details
Tags 基本上是键值对。在Pass内部，Tags 用于控制此pass在照明管线中的作用（环境，顶点照明，像素照明等）和其他一些选项。请注意，一下被Unity识别的 Tags 必须放在 pass 中，而不是在 **SubShader**！

#### LightMode tag
LightMode tag 定义 Pass 在 lighting pipeline 的角色。有关详细信息，参考[render pipeline](../../../../AdvancedShaderLabTopics/UnityRenderingPipeline/README.md)。这些标签很少手动使用，最常见的着色器“需要与照明交互”被编写为[Surface Shaders](../../../../WritingSurfaceShaders/README.md)，然后所有这些细节都被自动处理。

LightMode tag的可能值为：
* **Always**: 始终渲染；没有照明。
* **ForwardBase**: [Forward rendering](../../../../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)中使用，环境光、主平行光， vertex/SH 光源和 光照贴图被使用。
* **ForwardAdd**: [Forward rendering](../../../../../RenderingPipelineDetails/* ForwardRenderingPathDetails/README.md)中使用。附加逐顶点光源被使用，每个光源一个pass。
* **Deferred**:  [Deferred Shading](../../../../../RenderingPipelineDetails/DeferredShadingRenderingPath/README.md)中使用。渲染 g-buffer。
* **ShadowCaster**: 将对象深度渲染到阴影贴图或深度纹理中。
* **MotionVectors**: 用于计算每个对象的运动矢量。
* **PrepassBase**:  [legacy Deferred Lighting](../../../../../RenderingPipelineDetails/LegacyDeferredLightingRenderingPath/README.md)中使用。渲染法线和镜面指数。
* **PrepassFinal**:  [legacy Deferred Lighting](../../../../../RenderingPipelineDetails/LegacyDeferredLightingRenderingPath/README.md)中使用。通过组合纹理，照明和发射来渲染最终颜色。
* **Vertex**: [ legacy Vertex Lit rendering ](../../../../../RenderingPipelineDetails/VertexLitRenderingPathDetails/README.md)中使用。当对象没有光照贴图时，应用所有的顶点光源。
* **VertexLMRGBM**: [ legacy Vertex Lit rendering ](../../../../../RenderingPipelineDetails/VertexLitRenderingPathDetails/README.md)中使用。当对象有光照贴图时，在光照图是RGBM编码的平台上（PC和控制台）。
* **VertexLM**:  [ legacy Vertex Lit rendering ](../../../../../RenderingPipelineDetails/VertexLitRenderingPathDetails/README.md)中使用。当对象有光照贴图时，在光照图是双LDR编码的平台上（移动平台）。

#### PassFlags tag
pass能指定flags，改变[render pipeline](../../../../AdvancedShaderLabTopics/UnityRenderingPipeline/README.md)传递数据的方式。这是通过使用**PassFlags**标记完成的，该标记的值是用空格分隔的flag名称。当前支持的标志是：
* **OnlyDirectional**：在 [Forward rendering](../../../../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md) Pass 类型中使用时，使用此标志可以使其仅将主定向光照和环境/光照探针数据传递到着色器中。这意味着不重要的光的数据不会传递到顶点光或球谐函数着色器变量中。有关详细信息，请参见 [Forward rendering](../../../../../RenderingPipelineDetails/ForwardRenderingPathDetails/README.md)。

#### RequireOptions tag
pass可以表明仅应在满足某些外部条件时才进行渲染。这是pass使用**RequireOptions**标记完成的，该标记的值是一串用空格分隔的选项。当前Unity支持的选项包括：

* **SoftVegetation**：仅当[Quality](https://docs.unity3d.com/Manual/class-QualitySettings.html)窗口中的 Soft Vegetation 处于启用状态时，才渲染此 pass。

## See Also
还可以为SubShader提供标签，请参见 [SubShader Tags](../../ShaderLabPassSubShaderTags/README.md)。