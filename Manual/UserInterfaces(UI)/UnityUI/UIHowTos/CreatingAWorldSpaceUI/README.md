# [Creating a World Space UI](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/HOWTO-UIWorldSpace.html)
通过UI系统，可以轻松创建位于场景中其他2D或3D对象之间的世界中的UI。

如果您在场景中还没有UI元素（例如Image），请先使用**GameObject> UI> Image**创建一个UI元素（例如Image）。 这还将为您创建一个画布。

## Set the Canvas to World Space
选择您的画布并将渲染模式更改为世界空间。

现在，您的画布已经定位在世界中，并且如果所有的相机都指向它，便可以看到它，但是与场景中的其他对象相比，它可能很大。 我们将回到这一点。

## Decide on a resolution
首先，您需要确定Canvas的分辨率。 如果是图像，则图像的像素分辨率应该是多少？ 800x600之类的内容可能是一个不错的起点。 在“矩形”的“画布”变换的“宽度”和“高度”值中输入分辨率。 同时将位置设置为0,0可能是一个好主意。

## Specify the size of the Canvas in the world
现在，您应该考虑“画布”在世界上应该有多大。 您可以使用“缩放”工具简单地将其按比例缩小，直到看起来合适为止，或者可以决定以米为单位的大小。

如果希望它具有以米为单位的特定宽度，则可以使用meter_size / canvas_width计算所需的比例。 例如，如果您希望它的宽度为2米，而“画布”宽度为800，则将有2/800 = 0.0025。 然后，将“画布”上“矩形”变换的“缩放”属性对于X，Y和Z均设置为0.0025，以确保其均匀缩放。

另一种考虑方式是，您正在控制“画布”中一个像素的大小。 如果将Canvas缩放0.0025，则那也是Canvas中每个像素的大小。

## Position the Canvas
与“画布”设置为“屏幕空间”不同，“世界空间”画布可以在场景中自由定位和旋转。 您可以将Canvas放在任何墙壁，地板，天花板或倾斜的表面上（或自然悬挂在空中）。 只需使用工具栏中的常规“平移和旋转”工具即可。

## Create the UI
现在，您可以像使用“屏幕空间画布”一样开始设置UI元素和布局。