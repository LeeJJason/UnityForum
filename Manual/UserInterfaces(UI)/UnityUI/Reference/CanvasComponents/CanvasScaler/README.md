# [Canvas Scaler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-CanvasScaler.html)
Canvas Scaler组件用于控制Canvas中UI元素的整体比例和像素密度。 这种缩放会影响“画布”下的所有内容，包括字体大小和图像边框。  
![](UI_CanvasScalerInspector.png)

## Properties
|Property:|Function:|
|:--------|:--------|
|UI Scale Mode|确定Canvas中UI元素的缩放方式。|
|&emsp;Constant Pixel Size|无论屏幕大小如何，都使UI元素保持相同的像素大小。|
|&emsp;Scale With Screen Size|屏幕越大，UI元素越大。|
|&emsp;Constant Physical Size|使UI元素保留相同的物理尺寸，而不考虑屏幕尺寸和分辨率。|

**Constant Pixel Size**的设置：
|Property:|Function:
|:--------|:------
|Scale Factor|以此比例缩放Canvas中的所有UI元素。
|Reference Pixels Per Unit|如果sprite具有**Pixels Per Unit**设置，则sprite中的一个像素将覆盖UI中的一个单位。


**Scale With Screen Size**的设置：
|Property:|Function:
|:--------|:------
|Reference Resolution|UI布局的设计分辨率。 如果屏幕分辨率较大，则UI会放大；如果屏幕分辨率较小，则UI会缩小。
|Screen Match Mode|如果当前分辨率的宽高比不适合参考分辨率，则用于缩放画布区域的模式。
|&emsp;Match Width or Height|缩放画布区域，以宽度为参考，以高度为参考，或介于两者之间。
|&emsp;Expand|水平或垂直扩展画布区域，以使画布的大小永远不会小于参考值。
|&emsp;Shrink|水平或垂直裁剪画布区域，因此画布的大小永远不会大于参考值。
|Match|确定缩放比例是使用宽度还是高度作为参考，还是两者之间的混合。
|Reference Pixels Per Unit|如果sprite具有**Pixels Per Unit**设置，则sprite中的一个像素将覆盖UI中的一个单位。


**Constant Physical Size**的设置：
|Property:|Function:
|:--------|:------
|Physical Unit|指定位置和大小的物理单位。
|Fallback Screen DPI|假设屏幕DPI未知的DPI。
|Default Sprite DPI|具有“单位像素数”设置与“单位像素参考数”设置匹配的精灵的每英寸像素数。
|Reference Pixels Per Unit|如果子画面具有此**Pixels Per Unit**设置，则其DPI将与**Default Sprite DPI**设置匹配。

**World Space Canvas**的设置（当**Canvas**组件设置为**Canvas**时显示）：
|Property:|Function:
|:--------|:------
|Dynamic Pixels Per Unit|UI中动态创建的位图（例如文本）所使用的每单位像素数。
|Reference Pixels Per Unit|如果一个精灵具有此**Pixels Per Unit**设置，则该精灵中的一个像素将覆盖世界上的一个单位。 如果将**Reference Pixels Per Unit**设置为1，则子画面中的**Pixels Per Unit**设置将保持不变。

## Details
对于设置为**Screen Space - Overlay**或**Screen Space - Camera**的画布，可以将Canvas Scaler UI缩放模式设置为Constant Pixel Size，Scale With Screen Size， 或者 Constant Physical Size。

### Constant Pixel Size
使用**Constant Pixel Size**模式，可以在屏幕上以像素为单位指定UI元素的位置和大小。 当未连接Canvas Scaler时，这也是Canvas的默认功能。 但是，使用**Canvas Scaler**中的**Scale Factor**设置，可以将恒定缩放应用于Canvas中的所有UI元素。

### Scale With Screen Size
使用**Scale With Screen Size**模式，可以根据指定参考分辨率的像素指定位置和尺寸。 如果当前屏幕分辨率大于参考分辨率，则Canvas将保持仅具有参考分辨率的分辨率，但是会放大以适合屏幕。 如果当前屏幕分辨率小于参考分辨率，则Canvas将类似地缩小以适合。

如果当前屏幕分辨率的长宽比与参考分辨率不同，则单独缩放每个轴以适合屏幕将导致缩放不均匀，这通常是不希望的。 取而代之的是，ReferenceResolution组件将使Canvas分辨率偏离参考分辨率，以尊重屏幕的长宽比。 使用屏幕匹配模式设置可以控制这种偏差的表现方式。

### Constant Physical Size
使用**Constant Physical Size**模式，UI元素的位置和尺寸以物理单位（例如，毫米，点或皮卡）指定。 此模式取决于设备正确报告其屏幕DPI。 您可以指定后备DPI用于不报告DPI的设备。

### World Space
对于设置为**World Space**的Canvas，可以使用Canvas Scaler来控制Canvas中UI元素的像素密度。

## Hints
有关如何将Rect Transform锚定和Canvas Scaler结合使用以制作适合不同分辨率和纵横比的UI布局的逐步说明，请参阅[Designing UI for Multiple Resolutions](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/HOWTO-UIMultiResolution.html)页。