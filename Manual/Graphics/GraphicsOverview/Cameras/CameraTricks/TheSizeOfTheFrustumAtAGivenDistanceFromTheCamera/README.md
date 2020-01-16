## The Size of the Frustum at a Given Distance from the Camera
在距摄像机一定距离处的view frustum cross-section在世界空间中定义一个矩形框，框内为可见区域。有时计算给定距离下这个矩形的大小是很有用的，或者找出给定大小下这个矩形的距离。例如，如果一个移动的相机需要在任何时候都保持一个物体(比如玩家)完全在镜头内，那么它就不能太接近那个物体的一部分而被切断。

给定距离下的截体高度(均为世界单位)可由下式求得:
```CS
var frustumHeight = 2.0f * distance * Mathf.Tan(camera.fieldOfView * 0.5f * Mathf.Deg2Rad);
```

这个过程可以反过来计算距离需要给出一个指定的截锥体高度:
```CS
var distance = frustumHeight * 0.5f / Mathf.Tan(camera.fieldOfView * 0.5f * Mathf.Deg2Rad);
```

当高度和距离已知时，也可以计算FOV角:
```CS
var camera.fieldOfView = 2.0f * Mathf.Atan(frustumHeight * 0.5f / distance) * Mathf.Rad2Deg;
```

每一个计算都涉及到截锥体的高度，但这可以很容易地从宽度(反之亦然)得到:
```CS
var frustumWidth = frustumHeight * camera.aspect;
var frustumHeight = frustumWidth / camera.aspect;
```