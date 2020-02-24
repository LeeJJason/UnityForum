## Deferred shading rendering path
此页面详细说明了[deferred shading rendering path](../../../GraphicsOverview/AdvancedRenderingFeatures/RenderingPaths/README.md)。参见[Wikipedia: deferred shading](http://en.wikipedia.org/wiki/Deferred_shading)以获得介绍性的技术概述。

### Overview
当使用延迟着色时，可以影响游戏对象的灯光数量没有限制。所有的光都是按像素计算的，这意味着它们都能正确地与法线贴图交互等。此外，所有的灯光可以有cookies 和 shadows。

延迟着色的优点是光照的处理开销与光照像素的数量成正。这取决于场景中光量的大小不管它照亮了多少游戏物体。因此，性能可以通过保持小的光源来提高。延迟着色也有高度一致和可预测的行为。每个光的效果是按像素计算的，所以在大三角形上没有分解的照明计算。

缺点是，延迟着色没有真正的抗锯齿支持，并且不能处理半透明的游戏对象(这些是使用前向渲染呈现的)。也没有对网格的支持渲染器的接收阴影标记和筛选蒙版仅以有限的方式支持。您最多只能使用4个筛选掩码。也就是你的剔除图层蒙版必须至少包含所有层减去4个任意层，所以32个层中的28个必须设置。否则您将得到图形工件。

### Requirements
它需要一个图形卡支持多个渲染目标(MRT)，着色器Model 3.0(或更高版本)和对深度渲染纹理的支持。2006年后生产的大多数PC显卡都支持延迟着色，从GeForce 8xxx、Radeon X2400和Intel G45开始。

在移动设备上，所有运行至少OpenGL ES 3.0的设备都支持延迟着色。

注意:延迟渲染不支持 *Orthographic projection*。如果相机的投影模式设置为*Orthographic projection*，相机回落到前向渲染。

### Performance considerations
在延迟着色中，实时灯光的渲染开销与被灯光照亮的像素数量成正比，而不依赖于场景的复杂性。所以小点或聚光灯是非常便宜的渲染，如果他们是完全或部分闭塞的场景游戏对象，那么他们甚至更便宜。

当然，有阴影的光源比没有阴影的光源要贵得多。在延迟着色中，阴影投射游戏对象仍然需要为每个阴影投射光渲染一次或多次。此外，应用阴影的光照着色器比禁用阴影时使用的渲染开销更高。

### Implementation details
带有不支持延迟着色的着色器的对象在延迟着色完成后使用[forward rendering path](../ForwardRenderingPathDetails/README.md)。

下面列出了几何缓冲区(g-buffer)中呈现目标(RT0 - RT4)的默认布局。数据类型放在每个呈现目标的各种通道中。使用的通道显示在括号中。
* RT0, ARGB32 format: Diffuse color (RGB), occlusion (A).
* RT1, ARGB32 format: Specular color (RGB), roughness (A).
* RT2, ARGB2101010 format: World space normal (RGB), unused (A).
* RT3, ARGB2101010 (non-HDR) or ARGBHalf (HDR) format: Emission + lighting + lightmaps + reflection probes
 buffer.
* Depth+Stencil buffer.

因此，默认的g-buffer布局是160位/像素(非HDR)或192位/像素(HDR)。
如果使用[Shadowmask](https://docs.unity3d.com/Manual/LightMode-Mixed-Shadowmask.html)或[Distance Shadowmask](https://docs.unity3d.com/Manual/LightMode-Mixed-DistanceShadowmask.html)模式混合照明，第五个目标是使用:
* RT4, ARGB32 format: Light occlusion values (RGBA).

因此，g缓冲区的布局是192位/像素(非HDR)或224位/像素(HDR)。

如果硬件不支持第5个并发渲染目标，那么使用shadowmask的对象将退回到正向渲染路径。 Emission+lighting缓冲(RT3)是对数编码的，以提供比ARGB32纹理通常可能的更大的动态范围，当相机不使用HDR。

请注意，当相机使用HDR渲染时，没有为Emission+lighting buffer (RT3)创建单独的rendertarget;相反，摄像机呈现的rendertarget(即传递给图像效果的那个)被用作RT3。

#### G-Buffer pass
g-buffer pass 渲染每个GameObject一次。漫反射和高光颜色，表面平滑度，世界空间法线，和发射+环境+反射+光照贴图被渲染成g-buffer纹理。g缓冲纹理被设置为全局着色器属性，以便着色器访问(CameraGBufferTexture0 ..CameraGBufferTexture3名称)。


#### Lighting pass
照明 pass 根据g缓冲和深度计算照明。光照是在屏幕空间中计算的，因此处理所需的时间与场景的复杂性无关。照明被添加到发射缓冲器。

点和聚光灯，不跨越相机的近平面渲染为3D形状，与Z缓冲区的测试对场景启用。这使得部分或完全遮挡点和聚光灯渲染非常便宜。Directional lights 和 point/spot lights，跨近平面渲染为全屏四轴。

如果灯光启用了阴影，那么它们也会在这个通道中被渲染和应用。注意，影子不是为“免费”而来;阴影脚轮需要渲染和一个更复杂的光着色器必须应用。

唯一可用的照明模式是标准的。如果需要一个不同的模型，你可以修改照明通道着色器，通过放置内部延迟着色的修改版本。着色器文件从内置的着色器到一个文件夹命名为“Resources”在您的“Assets”文件夹。然后打开图形设置(菜单:**Edit > Project Settings**，然后点击图形类别)。将“延迟”下拉菜单改为“自定义着色器”。然后改变你使用的着色器的着色器选项。