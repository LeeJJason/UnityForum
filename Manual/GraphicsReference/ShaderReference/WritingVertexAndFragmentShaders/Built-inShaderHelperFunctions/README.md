## Built-in shader helper functions
Unity 有大量的内置工具函数，与设计来是写shader跟简单容易。

### Functions declared in UnityCG.cginc
有关Unity随附的着色器包含文件的概述，请参见[内置着色器包含文件](../Built-inShaderIncludeFiles/README.md)。

### Vertex transformation functions in UnityCG.cginc
|Function:	|Description:|
|:------|:---------------|
|float4 UnityObjectToClipPos(float3 pos)|在其次坐标中将对象空间中的点转换到摄像机的裁剪空间。这个和**mul(UNITY_MATRIX_MVP, float4(pos, 1.0))**相同，应该代替它使用。|
|float3 UnityObjectToViewPos(float3 pos)|	将点从对象空间转换为视图空间。这等效于**mul(UNITY_MATRIX_MV，float4(pos，1.0))**.xyz，应该代替它使用。|

### Generic helper functions in UnityCG.cginc
|Function:	|Description:|
|:------|:---------------|
|float3 WorldSpaceViewDir (float4 v)|返回给定的对象空间中的点到摄像机在世界坐标系中的方向（未归一化）|
|float3 ObjSpaceViewDir (float4 v)|	返回给定的对象空间中的点到摄像机在对象坐标系中的方向（未归一化）|
|float2 ParallaxOffset (half h, half height, half3 viewDir)	|计算视差法线贴图的UV偏移。|
|fixed Luminance (fixed3 c)	|将颜色转换为亮度（灰度）。|
|fixed3 DecodeLightmap (fixed4 color)|	解码来自Unity 光照贴图颜色（RGBM或dLDR，取决于平台）。|
|float4 EncodeFloatRGBA (float v)|	编码[0..1）范围浮动为RGBA颜色，以存储在低精度渲染目标中。|
|float DecodeFloatRGBA (float4 enc)	|将RGBA颜色解码为浮点型。|
|float2 EncodeFloatRG (float v)	|将[0..1）范围浮点数编码为float2。|
|float DecodeFloatRG (float2 enc)|	解码以前编码的RG float。|
|float2 EncodeViewNormalStereo (float3 n)|将视图空间法线编码为0..1范围内的两个数字。|
|float3 DecodeViewNormalStereo (float4 enc4)|从enc4.xy解码视图空间正常。|

## Forward rendering helper functions in UnityCG.cginc
这些功能仅在使用正向渲染（ForwardBase或ForwardAdd pass类型）时才有用。
|Function:	|Description:|
|:------|:---------------|
|float3 WorldSpaceLightDir (float4 v)|通过给定的对象空间的顶点坐标，计算世界空间中的到光源的方向（未归一化）|
|float3 ObjSpaceLightDir (float4 v)|通过给定的对象空间的顶点坐标，计算对象空间中的到光源的方向（未归一化）|
|float3 Shade4PointLights (...)|从四个点光源计算光照，并将光数据紧密打包到矢量中。前向渲染使用此来计算每个顶点的光照。|

## Screen-space helper functions in UnityCG.cginc
以下函数是计算坐标的助手，用于采样屏幕空间纹理。它们返回float4可以通过透视除法计算出样本纹理的最终坐标的位置（例如xy/w）。

这些功能还应注意渲染纹理坐标中[平台的差异](https://docs.unity3d.com/Manual/SL-PlatformDifferences.html)。
|Function:	|Description:|
|:------|:---------------|
|float4 ComputeScreenPos (float4 clipPos)|	计算纹理坐标以进行屏幕空间映射的纹理采样。输入为裁剪空间位置。|
|float4 ComputeGrabScreenPos (float4 clipPos)|	计算用于采样[GrabPass](https://docs.unity3d.com/Manual/SL-GrabPass.html)纹理的纹理坐标。输入为剪辑空间位置。|

## Vertex-lit helper functions in UnityCG.cginc
这些功能仅在使用逐顶点照明着色器（“顶点”传递类型）时才有用。
|Function:	|Description:|
|:------|:---------------|
|float3 ShadeVertexLights (float4 vertex, float3 normal)|在给定对象空间位置和法线的情况下，根据四个顶点照明和环境计算照明。|