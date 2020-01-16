## Rays from the Camera
在[Understanding the View Frustum](../UnderstandingTheViewFrustum/README.md)的部分中，解释了摄像机的视图中的任何一点对应于世界空间中的一条线。有时用数学方法来表示这条线是很有用的，Unity可以以[Ray](https://docs.unity3d.com/ScriptReference/Ray.html)对象的形式提供这个。射线总是对应于视图中的一个点，因此Camera类提供了[ScreenPointToRay](https://docs.unity3d.com/ScriptReference/Camera.ScreenPointToRay.html)和[ViewportPointToRay](https://docs.unity3d.com/ScriptReference/Camera.ViewportPointToRay.html)函数。两者的不同之处在于ScreenPointToRay希望以像素的形式提供点坐标，而ViewportPointToRay在范围0..1(其中0表示视图的底部或左侧，1表示视图的顶部或右侧)。每个函数都返回一条射线，射线由一个原点和一个向量组成，向量表示从原点出发的直线的方向。射线来源于**clipping plane**而不是相机的transform.position。

### Raycasting
来自摄像机的 Ray 最常见的用途是将 [raycast](https://docs.unity3d.com/ScriptReference/Physics.Raycast.html) 在场景中执行。一个raycast从它的起点沿着射线发射一个假想的“激光束”，直到它击中场景中的**collider**。然后返回关于对象和在[RaycastHit](https://docs.unity3d.com/ScriptReference/RaycastHit.html)对象中命中的点的信息。这是一个非常有用的方法来定位一个对象基于其屏幕上的形象。例如，鼠标位置的对象可以通过以下代码来确定:
*C# script example:*
```cs
sing UnityEngine;
using System.Collections;

public class ExampleScript : MonoBehaviour {
    public Camera camera;

    void Start(){
        RaycastHit hit;
        Ray ray = camera.ScreenPointToRay(Input.mousePosition);
        
        if (Physics.Raycast(ray, out hit)) {
            Transform objectHit = hit.transform;
            
            // Do something with the object that was hit by the raycast.
        }
    }
}
```
*JS script example:*
```js
var hit: RaycastHit;
var ray: Ray = camera.ScreenPointToRay(Input.mousePosition);

if (Physics.Raycast(ray, hit)) {
    var objectHit: Transform = hit.transform;
    
    // Do something with the object that was hit by the raycast.
}
```

## Moving the Camera Along a Ray
有时得到一条与屏幕位置相对应的光线，然后沿着这条光线移动摄像机是很有用的。例如，您可能希望允许用户使用鼠标选择一个对象，然后放大它，同时将其“固定”在鼠标下的相同屏幕位置(例如，当摄像机查看战术地图时，这可能很有用)。实现这一点的代码相当简单:
*C# script example:*
```cs
using UnityEngine;
using System.Collections;

public class ExampleScript : MonoBehaviour {
    public bool zooming;
    public float zoomSpeed;
    public Camera camera;

    void Update() {
        if (zooming) {
            Ray ray = camera.ScreenPointToRay(Input.mousePosition);
            float zoomDistance = zoomSpeed * Input.GetAxis("Vertical") * Time.deltaTime;
            camera.transform.Translate(ray.direction * zoomDistance, Space.World);
        }
    }
}
```

*JS script example:*
```js
var zooming: boolean;
var zoomSpeed: float;

if (zooming) {
    var ray: Ray = camera.ScreenPointToRay(Input.mousePosition);
    zoomDistance = zoomSpeed * Input.GetAxis("Vertical") * Time.deltaTime;
    camera.transform.Translate(ray.direction * zoomDistance, Space.World);
}
```