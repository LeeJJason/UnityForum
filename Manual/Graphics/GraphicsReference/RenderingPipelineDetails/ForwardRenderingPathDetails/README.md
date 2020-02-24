## Forward Rendering Path Details
此页面详细说明了[Forward rendering path](../../../GraphicsOverview/AdvancedRenderingFeatures/RenderingPaths/README.md)。

**Forward Rendering path**在一个或多个通道中渲染每个对象，这取决于影响对象的灯光。根据灯光的设置和强度，通过前向渲染，灯光本身也得到了不同的处理。


### Implementation Details
在前向渲染中，一些影响每个对象的最亮的灯光被渲染为完全逐像素照明模式。然后，每个顶点最多计算4个点灯。其他的光被计算为球面谐波(SH)，这是更快的，但只是一个近似值。一盏灯是否为逐像素灯取决于以下几点:
* 将渲染模式设置为不重要的光总是逐顶点或SH。
* 最亮的平行光源总是逐像素。
* 将渲染模式设置为重要的光总是逐像素。
* 如果以上导致光源少于**Pixel Light Count**[Quality Setting](https://docs.unity3d.com/Manual/class-QualitySettings.html)，然后更多的灯渲染逐像素，以减少亮度。

每个对象的渲染过程如下:
* Base Pass应用一个像素方向光和所有顶点/SH光。
* 其他每像素的光被渲染在额外的Pass中，每一个Pass代表一个光。

例如，如果有一个物体受到多个光的影响(下图中的一个圆圈，受到a到H光的影响):
![](ForwardLightsExample.png)

让我们假设灯光A到H有相同的颜色和强度，并且它们都有自动渲染模式所以它们会按照这个物体的顺序排列。最亮的灯光将在逐像素照明模式(A到D)中渲染，然后在逐顶点照明模式(D到G)中渲染最多4个灯光，最后在SH (G到H)中渲染剩下的灯光:
![](ForwardLightsClassify.png)

注意光组重叠;例如，最后的每个像素的光混合到每个顶点的光照模式中，这样当物体和光线移动时，就会有更少的“light popping”。

#### Base Pass
Base pass渲染对象，每个像素一个方向光和所有SH/顶点光。这个通道还添加了所有的光照贴图，环境和发射光从着色器。在这个通道中渲染的方向光可以有阴影。请注意，lightmapping对象不会从SH灯获得照明。

注意，当在着色器中使用“only direction”传递标志时，前向基遍只呈现主方向光、环境光探测和光映射(SH和顶点光不包含在传递数据中)。

#### Additional Passes
每增加一个影响该对象的每像素光都会呈现额外的通道。这些光在默认情况下没有阴影(因此，前向渲染支持一个有阴影的方向光)，除非使用**multi_compile_fwdadd_fullshadows**变体快捷方式。

### Performance Considerations
球面谐光渲染非常快。它们在CPU上的开销很小，而且实际上GPU可以免费使用(也就是说，base pass总是计算SH照明;但是由于SH灯的工作方式，无论有多少SH灯，成本都是完全相同的)。

SH灯的缺点有:
* 它们是在对象的顶点上计算的，而不是在像素上。这意味着它们不支持光cookie或法线贴图。
* SH照明的频率很低。你不能用SH灯来实现锐利的灯光过渡。它们也只影响漫射照明(对于高光来说频率太低)。
* SH照明不是本地的;点或点SH灯接近一些表面将“看起来错误”。

总之，SH灯对于小型动态对象来说通常足够好了。