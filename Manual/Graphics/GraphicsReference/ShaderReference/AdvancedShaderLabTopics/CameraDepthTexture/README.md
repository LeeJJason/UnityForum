## Camera’s Depth Texture
一个[Camera](https://docs.unity3d.com/Manual/class-Camera.html)可以生成depth, depth+normals, 或者 motion vector纹理。这是一个最小化的G-buffer纹理，可用于后期处理效果或实现自定义照明模型(如光预通过)。你自己也可以建立类似的纹理，使用[Shader Replacement](../RenderingWithReplacedShaders/README.md)功能。

相机的深度纹理模式可以使用[Camera.depthtexturemode](https://docs.unity3d.com/ScriptReference/Camera-depthTextureMode.html)变量从脚本中启用。

有三种可能的深度纹理模式:
* DepthTextureMode.Depth: [depth texture](../UsingDepthTextures/README.md)。
* DepthTextureMode.DepthNormals: 深度和视图空间的法线压缩到一个纹理。
* DepthTextureMode.MotionVectors: 当前帧的屏幕的每个texel的逐像素屏幕空间运动。压缩成RG16的纹理。

这些是标志，所以可以指定上面纹理的任何组合。

### DepthTextureMode.Depth texture
这个构建一个屏幕大小的 [depth texture](../UsingDepthTextures/README.md)。
深度纹理使用相同的着色器渲染用于阴影施法者渲染的通道(ShadowCaster通过类型)。因此，扩展一下，如果一个着色器不支持阴影投射(即在着色器中没有阴影投射者通道或任何回退)，那么使用该着色器的对象将不会出现在深度纹理中。
* 使你的着色器退到其他有阴影投射通道的着色器
* 如果你使用表面着色器，添加一个addshadow指令将使他们产生一个影子pass。

注意，只有“不透明”的对象(材质和着色器设置为使用[render queue](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPassSubShaderTags/README.md)<= 2500的对象)被渲染到深度纹理中。

### DepthTextureMode.DepthNormals texture
这将构建一个屏幕大小的32位(8 bit/channel)纹理，其中视图空间法线编码到R&G通道中，深度编码到B&A通道中。法线是使用立体投影编码的，深度是16位值，被压缩到两个8位通道中。

[UnityCG.cginc包含文件](../../WritingVertexAndFragmentShaders/Built-inShaderIncludeFiles/README.md)有一个助手函数`DecodeDepthNormal`来从编码的像素值解码depth 和 normal来。返回深度为0..1范围。
有关如何使用深度和法线纹理的示例，请参阅着色器替换示例项目中的边缘检测图像效果或[Screen Space Ambient Occlusion Image Effect](https://docs.unity3d.com/Manual/PostProcessing-AmbientOcclusion.html)。

### DepthTextureMode.MotionVectors texture
这将构建一个屏幕大小的RG16(16-bit float/channel)纹理，其中屏幕空间像素运动被编码到R&G通道中。像素运动被编码在屏幕UV空间中。
当从这个纹理运动中对编码的像素进行采样时，rance值为-1 ..1。这是上一帧到当前帧的UV偏移量。


## Tips & Tricks
[Camera inspector](https://docs.unity3d.com/Manual/class-Camera.html)指示当一个相机渲染一个depth 或者 depth+normals纹理。

从相机 ([Camera.depthTextureMode](https://docs.unity3d.com/ScriptReference/Camera-depthTextureMode.html)) 请求深度纹理的方式可能意味着在禁用了需要它们的效果之后，相机可能仍然继续渲染它们。如果在一个相机上有多个效果，其中每个都需要深度纹理，如果你禁用单独的效果，就没有好的方法来自动禁用深度纹理渲染。

在实现复杂的着色器或图像效果时，请记住[Rendering Differences Between Platforms](../PlatformSpecificRenderingDifferences/README.md)。特别是，在图像效果中使用深度纹理通常需要在Direct3D + Anti-Aliasing 进行特殊处理。

在某些情况下，深度纹理可能直接来自本地Z缓冲区。如果您在深度纹理中看到了工件，请确保使用它的着色器没有写入到Z缓冲区中(使用[ZWrite Off](../../ShaderLabSyntax/ShaderLabSubShader/ShaderLabPass/ShaderLabCullingDepthTesting/README.md))。

## Shader variables
深度纹理可以作为全局着色器属性在着色器中采样。通过声明一个名为`_CameraDepthTexture`的采样器，您将能够对相机的主深度纹理进行采样。

`_CameraDepthTexture`总是指相机的主要深度纹理。相反，你可以使用`_LastCameraDepthTexture`来引用任何相机最后渲染的深度纹理。这可能是有用的，例如，如果你使用一个次要的相机在脚本中渲染一个半分辨率的深度纹理，并希望它可以用于一个后期着色器。

运动矢量纹理(启用时)在着色器中作为全局着色器属性可用。通过声明一个叫做“_CameraMotionVectorsTexture”的采样器，你可以为这个普通渲染的相机采样纹理。

## Under the hood
深度纹理可以直接来自实际的深度缓冲区，也可以在单独的通道中渲染，这取决于使用的渲染路径和硬件。通常当使用递延阴影或递延灯光渲染路径时，深度纹理是 “for free”，因为它们是G-buffer渲染的产物。

当DepthNormals纹理在一个单独的通道中渲染时，这是通过着色器替换完成的。因此在着色器中使用正确的“RenderType”标签是很重要的。

当启用时，MotionVectors纹理总是来自额外的渲染通道。Unity会将移动的GameObjects渲染到这个缓冲区中，并构造它们从上一帧到当前帧的运动。

## See also
* [Post Processing package](https://docs.unity3d.com/Packages/com.unity.postprocessing@2.3/manual/index.html)
* [Depth Textures](../UsingDepthTextures/README.md)
* [Shader Replacement](../RenderingWithReplacedShaders/README.md)