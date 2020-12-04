# [Rect Transform](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/class-RectTransform.html)
**Rect Transform**组件是[**Transform**](https://docs.unity3d.com/Manual/class-Transform.html)组件的2D布局副本。其中**Transform**表示一个点，**Rect Transform**表示一个可以将UI元素放置在其中的矩形。如果Rect Transform的父对象也是Rect Transform，则子Rect Transform也可以指定相对于父矩形的放置方式和大小。  
![](UI_RectTransform.png)

## Properties
|**Property:**|**Function:**
|:------------|:------------
|**Pos (X, Y, Z)**|矩形的枢轴点相对于锚点的位置。枢轴点是矩形旋转所围绕的位置。
|**Width/Height**|矩形的宽度和高度。
|**Left, Top, Right, Bottom**|矩形边缘相对于其锚点的位置。可以将其视为锚点定义的矩形内的填充。当锚点分开时，显示在**Pos**和**Width/Height**位置（见下文）。要访问这些选项，请单击RectTransform组件左上方的方形**Anchor Presets**框。
|**Anchors**|矩形左下角和右上角的锚点。
|&nbsp;&nbsp;&nbsp;&nbsp;**Min**|矩形左下角的锚点定义为父矩形大小的一部分。 0,0对应于锚定到父级的左下角，而1,1对应于锚定到父级的右上角。
|&nbsp;&nbsp;&nbsp;&nbsp;**Max**|矩形右上角的锚点定义为父矩形大小的一部分。 0,0对应于锚定到父级的左下角，而1,1对应于锚定到父级的右上角。
|**Pivot**|矩形旋转所围绕的枢轴点的位置，定义为矩形本身大小的一部分。 0,0对应于左下角，而1,1对应于右上角。
|**Rotation**|围绕X，Y和Z轴的对象围绕其枢轴点的旋转角度（以度为单位）。
|**Scale**|在X，Y和Z维度上应用于对象的比例因子。
|**Blueprint Mode**|编辑RectTransforms，就好像它们没有旋转和缩放一样。这也启用了捕捉。
|**Raw Edit Mode**|启用后，编辑枢轴和锚点值将不会抵消调整矩形的位置和大小以使其停留在一个位置。

## Details
请注意，在计算UI顶点之前，某些RectTransform计算是在帧的末尾执行的，以确保它们与框架中执行的所有最新更改保持最新。这意味着尚未在Start回调和first Update回调中首次计算它们。

您可以通过创建`Start（）`回调并向其添加`Canvas.ForceUpdateCanvases（）`方法来解决此问题。
这将强制Canvas不在帧末尾更新，而是在调用该方法时更新。

有关如何使用Rect Transform的完整介绍和概述，请参见[Basic Layout](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/UIBasicLayout.html)页面。