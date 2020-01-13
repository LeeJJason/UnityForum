## Unity’s Rendering Pipeline
Shaders 定义了一个物体本身的样子(它的材质属性)和它对光线的反应。因为光照计算必须内置到着色器中，并且有许多可能的光和阴影类型，编写高质量的起作用的shaders一个复杂的任务。为了使之更容易，Unity 有 [Surface Shaders](../../WritingSurfaceShaders/README.md)，其中所有的照明，阴影，光照映射，正向和递延渲染事情都是自动处理的。

这个文档描述了Unity的 lighting & rendering pipeline 的细节和[Surface Shaders](../../WritingSurfaceShaders/README.md)幕后发生的事情。

### Rendering Paths
光照如何应用和shader 的哪些[Passes](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/README.md)，取决于使用哪个[Rendering Path](https://docs.unity3d.com/Manual/RenderingPaths.html)。shader中的每个pass与光照类型的联系是通过[Pass Tags](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/ShaderLabPassTags/README.md)确定的。