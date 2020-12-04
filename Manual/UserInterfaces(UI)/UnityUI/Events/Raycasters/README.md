# Raycasters
事件系统需要一种方法来检测当前输入事件需要发送到的位置，这是由Raycasters提供的。给定屏幕空间位置，他们将收集所有潜在目标，找出它们是否在给定位置之下，然后返回最靠近屏幕的对象。提供了几种类型的Raycaster：
* [Graphic Raycaster](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-GraphicRaycaster.html) - Used for UI elements, lives on a Canvas and searches within the canvas
* [Physics 2D Raycaster](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-Physics2DRaycaster.html) - Used for 2D physics elements
* [Physics Raycaster](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-PhysicsRaycaster.html) - Used for 3D physics elements

当存在Raycaster并将其启用在场景中时，无论何时从输入模块发出查询，事件系统都会使用它。

如果使用了多个Raycaster，则它们都将发生投射，并根据与元素的距离对结果进行排序。