# Event System
**Event System**是一种基于输入（例如键盘，鼠标，触摸或自定义输入）向应用程序中的对象发送事件的方法。**Event System**由一些共同协作以发送事件的组件组成。

将**Event System**组件添加到GameObject时，您会注意到它没有太多功能公开，这是因为**Event System**本身被设计为**Event System**模块之间通信的管理器和促进器。

事件系统的主要角色如下：
* Manage which GameObject is considered selected
* Manage which Input Module is in use
* Manage Raycasting (if required)
* Updating all Input Modules as required

## Input Modules
输入模块是事件系统如何运作的主要逻辑所在，它们用于：
* Handling Input
* Managing event state
* Sending events to scene objects.

一次只能在事件系统中激活一个输入模块，并且它们必须是与事件系统组件在同一GameObject上的组件。

如果要编写自定义输入模块，请发送Unity中现有UI组件支持的事件。要扩展和编写自己的事件，请参阅[消息系统](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/MessagingSystem.html)文档。

## Raycasters
Raycaster用于计算指针的位置。输入模块通常使用场景中配置的Raycaster来计算定点设备结束的时间。

默认情况下，存在3个提供的Raycasters：
* Graphic Raycaster - Used for UI elements
* Physics 2D Raycaster - Used for 2D physics elements
* Physics Raycaster - Used for 3D physics elements

如果在场景中配置了2d / 3d Raycaster，则很容易使非UI元素从输入模块接收消息。只需附加一个实现事件接口之一的脚本。有关此示例，请参见[IPointerEnterHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerEnterHandler.html)和https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerClickHandler.html
脚本参考页。