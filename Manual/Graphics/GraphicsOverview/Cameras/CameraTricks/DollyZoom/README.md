## Dolly Zoom (AKA the “Trombone” Effect)
多莉变焦是著名的视觉效果所在的相机同时向目标物体移动并从目标物体缩小。结果是，该对象看起来大致相同的大小，但所有其他对象在场景中变为透视。巧妙地做，多莉变焦有突出的目标对象的效果，因为它是唯一的东西在场景中没有移动的位置在图像。另一种选择是，可以故意快速地执行缩放，以创建迷失方向的印象。
一个垂直放置在截锥体中的对象将占据整个视图的高度，就像在屏幕上看到的那样。这是真的，无论物体到摄像机的距离是多少，无论视野是什么。例如，您可以将相机移动到更靠近对象的地方，然后扩大视野，使对象仍然适合frustum的高度。那个特定的对象会在屏幕上显示相同的大小，但是其他的东西会随着距离和FOV的变化而改变大小。这就是多莉变焦效果的精髓所在。
![](EqualFrustumHeight.png)

在代码中创建效果就是在缩放开始时保存对象位置的截锥体的高度。然后，当相机移动时，它的新距离被找到，FOV被调整以保持在物体位置的相同高度。这可以通过以下代码实现:
```cs
using UnityEngine;
using System.Collections;

public class ExampleScript : MonoBehaviour {
    public Transform target;
    public Camera camera;

    private float initHeightAtDist;
    private bool dzEnabled;

    // Calculate the frustum height at a given distance from the camera.
    float FrustumHeightAtDistance(float distance)
    {
        return 2.0f * distance * Mathf.Tan(camera.fieldOfView * 0.5f * Mathf.Deg2Rad);
    }

    // Calculate the FOV needed to get a given frustum height at a given distance.
    float FOVForHeightAndDistance(float height, float distance)
    {
        return 2.0f * Mathf.Atan(height * 0.5f / distance) * Mathf.Rad2Deg;
    }

    // Start the dolly zoom effect.
    void StartDZ()
    {
        var distance = Vector3.Distance(transform.position, target.position);
        initHeightAtDist = FrustumHeightAtDistance(distance);
        dzEnabled = true;
    }

    // Turn dolly zoom off.
    void StopDZ()
    {
        dzEnabled = false;
    }

    void Start()
    {
        StartDZ();
    }

    void Update()
    {
        if (dzEnabled)
        {
            // Measure the new distance and readjust the FOV accordingly.
            var currDistance = Vector3.Distance(transform.position, target.position);
            camera.fieldOfView = FOVForHeightAndDistance(initHeightAtDist, currDistance);
        }

        // Simple control to allow the camera to be moved in and out using the up/down arrows.
        transform.Translate(Input.GetAxis("Vertical") * Vector3.forward * Time.deltaTime * 5f);
    }
}
```
*C# script example*
```js
var target: Transform;

private var initHeightAtDist: float;
private var dzEnabled: boolean;


// Calculate the frustum height at a given distance from the camera.
function FrustumHeightAtDistance(distance: float) {
    return 2.0 * distance * Mathf.Tan(camera.fieldOfView * 0.5 * Mathf.Deg2Rad);
}


// Calculate the FOV needed to get a given frustum height at a given distance.
function FOVForHeightAndDistance(height: float, distance: float) {
    return 2 * Mathf.Atan(height * 0.5 / distance) * Mathf.Rad2Deg;
}


// Start the dolly zoom effect.
function StartDZ() {
    var distance = Vector3.Distance(transform.position, target.position);
    initHeightAtDist = FrustumHeightAtDistance(distance);
    dzEnabled = true;
}


// Turn dolly zoom off.
function StopDZ() {
    dzEnabled = false;
}


function Start() {
    StartDZ();
}


function Update () {
    if (dzEnabled) {
        // Measure the new distance and readjust the FOV accordingly.
        var currDistance = Vector3.Distance(transform.position, target.position);
        camera.fieldOfView = FOVForHeightAndDistance(initHeightAtDist, currDistance);
    }
    
    // Simple control to allow the camera to be moved in and out using the up/down arrows.
    transform.Translate(Input.GetAxis("Vertical") * Vector3.forward * Time.deltaTime * 5);
}

```
*JS script example*