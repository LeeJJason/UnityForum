## Custom lighting models in Surface Shaders
当写  Surface Shaders时，需要定义表面属性(例如 albedo 和 normal)和用于计算光照交互的 Lighting Model。

这儿有两种内置的光照模型：Lambert 用于漫反射光照和 BlinnPhong 用于高光光照。Unity里的Lighting.cginc 文件定义了这些模型(Windows: <unity install path>/Data/CGIncludes/Lighting.cginc; macOS: /Applications/Unity/Unity.app/Contents/CGIncludes/Lighting.cginc)。

有时可能想要自定义一个光照模型。 Surface Shaders 能这样做。一个光照模型仅仅是几个符合一些规定的 Cg/HLSL 函数。

## Declaring lighting models
光照模型以Lighting开头的常规函数构成。你能在自己的shader文件或者一个应用的文件的任意位置声明。这些功能是：
1. half4 Lighting< Name > (SurfaceOutput s, UnityGI gi); 在不依赖于视线方向的向前渲染路径中使用。
2. half4 Lighting< Name > (SurfaceOutput s, half3 viewDir, UnityGI gi); 在依赖于视线方向的向前渲染路径中使用。
3. half4 Lighting< Name >_Deferred (SurfaceOutput s, UnityGI gi, out half4 outDiffuseOcclusion, out half4 outSpecSmoothness, out half4 outNormal); 在延迟渲染路径中使用。
4. half4 Lighting< Name >_PrePass (SurfaceOutput s, half4 light); 在光预处理（旧式延迟）照明路径中使用此功能。

注意，你不需要声明所有的函数。一个光照模型使用视线方向或者不使用。相同的，如果光照模型仅仅工作在向前渲染，就不要声明 _Deferred 或者 _Prepass 函数。这确定使用的 Shaders 只编译向前渲染。

## Custom GI
声明下面的函数来自定义解码 lightmap 和 probes：
`half4 Lighting<Name>_GI (SurfaceOutput s, UnityGIInput data, inout UnityGI gi);`

请注意，要解码标准的Unity光照贴图和SH探测器，您可以使用内置函数DecodeLightmap和ShadeSHPerPixel功能，如Unity内部UnityGI_Base的UnityGlobalGlobalIllumination.cginc文件中所示（Windows：<unity安装路径> /Data/CGIncludes/UnityGlobalIllumination.cginc ; macOS ：/Applications/Unity/Unity.app/Contents/CGIncludes/UnityGlobalIllumination.cginc _）。

## Examples