## Built-in shader variables
内置着色器变量
Unity为着色器提供了一些内置的全局变量：诸如当前对象的变换矩阵，光照参数，当前时间等之类的东西。您可以像其他变量一样在着[色器程序](../README.md)中使用它们，唯一的区别是您不必声明它们-它们都在UnityShaderVariables.cginc自动包含的包含文件中声明。

## Transformations
所有这些矩阵都是float4x4类型。
|Name	|Value|
|:------|:---------------|
|UNITY_MATRIX_MVP|当前模型*视图*投影矩阵。|
|UNITY_MATRIX_MV|当前模型*视图矩阵。|
|UNITY_MATRIX_V|当前视图矩阵。|
|UNITY_MATRIX_P|当前投影矩阵。|
|UNITY_MATRIX_VP|当前视图*投影矩阵。|
|UNITY_MATRIX_T_MV|模型*视图矩阵的转置。|
|UNITY_MATRIX_IT_MV|模型*视图矩阵的逆转置。|
|_Object2World|当前模型矩阵。|
|_World2Object|当前世界矩阵的逆。|

## Camera and screen
这些变量将对应于正在渲染的[Camera](https://docs.unity3d.com/Manual/class-Camera.html)。例如，在阴影贴图渲染过程中，它们仍将引用Camera组件值，而不是用于阴影贴图投影的“虚拟相机”。
|Name	| Type | Value          |
|:------|:-----|:---------------|
|_WorldSpaceCameraPos|float3|相机的世界空间位置|
|_ProjectionParams|float4|x为1.0（如果当前使用[翻转的投影矩阵](https://docs.unity3d.com/Manual/SL-PlatformDifferences.html)进行渲染，则为–1.0 ），y相机的近平面，z相机的远平面和w是1 / FarPlane|
|_ScreenParams|float4|x是相机的渲染目标宽度（以像素为单位），y是相机的渲染目标高度（以像素为单位），z是1.0 + 1.0 /宽度和w是1.0 + 1.0 /高度|
|_ZBufferParams|float4|用于线性化Z缓冲区值。x是（1-far / near），y是（far / near），z是（x / far）和w是（y / far）|
|unity_OrthoParams|float4|x是正交摄影机的宽度，y是正交摄影机的高度，z未使用，w当正交摄影机时为1.0，透视时为0.0|
|unity_CameraProjection|float4x4|相机的投影矩阵|
|unity_CameraInvProjection|float4x4|相机投影矩阵的逆|
|unity_CameraWorldClipPlanes [6]|float4|相机视锥平面世界空间方程，按此顺序：左，右，下，上，近，远|

## Time
|Name	| Type | Value          |
|:------|:-----|:---------------|
|_Time|float4|自从场景加载以来的时间（t / 20，t，t * 2，t * 3），用于对着色器中的事物进行动画处理。|
|_SinTime|float4|时间正弦值：（t / 8，t / 4，t / 2，t）。|
|_CosTime|float4|时间的余弦：（t / 8，t / 4，t / 2，t）。|
|unity_DeltaTime|float4|增量时间：（dt，1 / dt，smoothDt，1 / smoothDt）。|


## Lighting
根据使用的[渲染路径](https://docs.unity3d.com/Manual/RenderingPaths.html)和在着色器中使用的[LightMode Pass](https://docs.unity3d.com/Manual/SL-PassTags.html)标签，以不同的方式将光参数传递给着色器。

[正向渲染](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html)（ForwardBase和ForwardAdd通过类型）：
|Name	| Type | Value          |
|:------|:-----|:---------------|
|_LightColor0 （在Lighting.cginc中声明）|fixed4|光源颜色|
|_WorldSpaceLightPos0|float4|方向灯：（世界空间方向，0）。其他灯：（世界空间位置1）。|
|_LightMatrix0 （在AutoLight.cginc中声明）|float4x4|世界到光的矩阵。用于采样Cookie和衰减纹理。|
|unity_4LightPosX0，unity_4LightPosY0，unity_4LightPosZ0|float4|（仅ForwardBase pass）前四个非重要点光源的世界空间位置。|
|unity_4LightAtten0|float4|（仅ForwardBase pass）前四个非重要点光源的衰减因子。|
|unity_LightColor|half4 [4]|（仅ForwardBase pass）前四个不重要的点光源的颜色。|

灯光pass着色器（全部在UnityDeferredLibrary.cginc中声明）中使用的延迟着色和延迟照明：
|Name	| Type | Value          |
|:------|:-----|:---------------|
|_LightColor|float4|光源颜色|
|_LightMatrix0|float4x4|世界到光的矩阵。用于采样Cookie和衰减纹理。|

球面谐波系数（由环境和光探针使用）都设置了ForwardBase，PrePassFinal并Deferred pass类型。它们包含3阶SH由世界空间正常进行计算（见ShadeSH9从UnityCG.cginc）。变量都是half4类型，unity_SHAr并且名称相似。

[Vertex-lit rendering](https://docs.unity3d.com/Manual/RenderTech-VertexLit.html) (Vertex pass type):
Vertex通过类型最多可设置8个灯；总是从最亮的开始排序。因此，如果要一次渲染受两个灯光影响的对象，则只需在数组中获取前两个条目。如果影响对象的光少于8个，其余的颜色将设置为黑色。
|Name	| Type | Value          |
|:------|:-----|:---------------|
|unity_LightColor|half4 [8]|光源颜色|
|unity_LightPosition|float4 [8]|视野光位置。（-direction，0）用于定向灯；（位置1）用于点/点光源。|
|unity_LightAtten|half4 [8]|光衰减系数。x为cos（spotAngle / 2）或–1（非点光源）；y为1 / cos（spotAngle / 4）或1为非点光源；z是二次衰减；w是光线范围的平方。|
|unity_SpotDirection|float4 [8]|视图空间聚光灯位置；（0,0,1,0）用于非聚光灯。|

## Fog and Ambient
|Name	| Type | Value          |
|:------|:-----|:---------------|
|unity_AmbientSky|fixed4|渐变环境照明条件中的天空环境照明颜色。|
|unity_AmbientEquator|fixed4|渐变环境照明条件中的赤道环境照明颜色。|
|unity_AmbientGround|fixed4|渐变环境照明条件中的地面环境照明颜色。|
|UNITY_LIGHTMODEL_AMBIENT|fixed4|环境照明颜色（渐变环境中的天空颜色）。旧版变量。|
|unity_FogColor|fixed4|雾色。|
|unity_FogParams|float4|雾计算参数：（密度/ sqrt（ln（2）），密度/ ln（2），– 1 /（结束起点），结束/（结束起点））。x用于Exp2雾模式，y用于Exp模式，z和w用于线性模式。|

## Various
|Name	| Type | Value          |
|:------|:-----|:---------------|
|unity_LODFade|float4|使用[LODGroup](https://docs.unity3d.com/Manual/class-LODGroup.html)时Level-of-detail 淡入。x是淡入淡出（0..1），y是淡入淡出量化为16个级别，z和w未使用。|
|_TextureSampleAdd|float4|仅对UNITY UI自动设置根据使用的纹理是否为Alpha8格式（该值设置为（1,1,1,0））（不设置为（0,0,0,0））来）。|