## Using more than one camera

创建一个Unity场景只包含一个摄像头这就是大多数情况下你所需要的。然而，你可以在一个场景中拥有任意数量的相机，它们的视图可以以不同的方式组合，如下所述。

### Switching cameras
默认情况下，摄像机呈现其视图以覆盖整个屏幕，因此一次只能看到一个摄像机视图(可视摄像机的深度属性值最高)。通过禁用一个相机并从脚本中启用另一个相机，您可以从一个相机“切换”到另一个相机，以提供一个场景的不同视图。您可以这样做，例如，在地图视图和第一人称视图之间进行切换。
```cs
using UnityEngine;
using System.Collections;

public class ExampleScript : MonoBehaviour {
    public Camera firstPersonCamera;
    public Camera overheadCamera;

    public void ShowOverheadView() {
        firstPersonCamera.enabled = false;
        overheadCamera.enabled = true;
    }
    
    public void ShowFirstPersonView() {
        firstPersonCamera.enabled = true;
        overheadCamera.enabled = false;
    }
}
```
*Example C# script*
```js
var firstPersonCamera: Camera;
var overheadCamera: Camera;

function ShowOverheadView() {
    firstPersonCamera.enabled = false;
    overheadCamera.enabled = true;
}

function ShowFirstPersonView() {
    firstPersonCamera.enabled = true;
    overheadCamera.enabled = false;
}
```
*Example JS script*

### Rendering a small camera view inside a larger one
通常，您希望至少有一个摄像机视图覆盖整个屏幕(默认设置)，但是在屏幕的小区域内显示另一个视图通常很有用。例如，您可能会在驾驶游戏中显示后视镜，或者在主视图为第一人称的情况下在屏幕的角落中显示头顶上的迷你地图。可以使用Viewport Rect属性设置摄像机屏幕上矩形的大小。

视口的坐标矩形相对于屏幕是“标准化”的。底部和左侧的坐标是0.0，而顶部和右侧的坐标是1.0。坐标值是0.5的一半。除了视口大小之外，您还应该将视图较小的摄像机的深度属性设置为比背景摄像机更高的值。确切的值并不重要，但规则是，具有较高深度值的相机比具有较低深度值的相机更容易呈现。