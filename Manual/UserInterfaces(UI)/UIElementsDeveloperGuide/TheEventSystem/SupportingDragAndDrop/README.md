# Supporting drag and drop 
要实现拖放功能，您需要确保放置区域VisualElements和可拖动的VisualElements注册特定事件的回调。
本页讨论这些VisualElement接收事件时发生的情况。

有关事件的更多信息，请参阅[事件系统上的文档](https://docs.unity3d.com/Manual/UIE-Events.html)。

## Registering callbacks for drop areas
作为放置区域的VisualElement需要注册以下五种事件类型的回调。

### DragEnterEvent
当用户拖动可拖动对象时，指针进入VisualElement时，将发送[DragEnterEvent](https://docs.unity3d.com/ScriptReference/UIElements.DragEnterEvent.html)。

当放置区域VisualElement收到DragEnterEvent时，它需要提供反馈，让用户知道它或其子对象是潜在放置操作的目标。

例如，可以通过将[USS](https://docs.unity3d.com/Manual/UIE-USS.html)类添加到目标元素，并在鼠标指针下方显示被拖动对象的“重影”来实现此目的。

### DragLeaveEvent
当用户拖动可拖动对象时，指针退出VisualElement时，将发送[DragLeaveEvent](https://docs.unity3d.com/ScriptReference/UIElements.DragLeaveEvent.html)。

当放置区域VisualElement收到DragLeaveEvent时，它需要停止提供放置反馈。

例如，可以删除目标元素收到DragEnterEvent时添加的USS类，而不再显示所拖动对象的“鬼影”，从而实现此目的。

### DragUpdatedEvent
当用户拖动可拖动对象时，当指针移到VisualElement上时，将发送[DragUpdatedEvent](https://docs.unity3d.com/ScriptReference/UIElements.DragUpdatedEvent.html)。

当放置区域VisualElement收到DragUpdatedEvent时，它需要更新放置反馈。

例如，可以通过移动所拖动对象的“重影”来使其保持在鼠标指针下方。

放置区域VisualElement还应该检查DragAndDrop属性，并设置DragAndDrop.visualMode以指示放置操作的效果。
例如，放置操作可以创建新对象，移动现有对象，拒绝放置操作等等。

### DragPerformEvent
当用户拖动任何可拖动对象并在VisualElement上释放鼠标指针时，将发送[DragPerformEvent](https://docs.unity3d.com/ScriptReference/UIElements.DragPerformEvent.html)。
仅当VisualElement将DragAndDrop.visualMode设置为DragAndDropVisualMode.None或DragAndDropVisualMode.Rejected以外的其他值时，才表示它可以接受拖动的对象。

当放置区域VisualElement收到DragPerformEvent时，需要对存储在DragAndDrop.objectReferences，DragAndDrop.paths或DragAndDrop.GetGenericData（）中的拖动对象采取适当的操作。

例如，它可能会在用户放置对象的位置添加新的VisualElement。

### DragExitedEvent
当用户在VisualElement上拖动任何可拖动对象并释放鼠标指针时，将发送[DragExitedEvent](https://docs.unity3d.com/ScriptReference/UIElements.DragExitedEvent.html)。
仅当没有VisualElement指示它可以接受拖动的对象时，才会发生这种情况。

当放置区域VisualElement收到DragExitedEvent时，它需要删除所有拖放反馈。

注意：UIElements当前存在一个错误，该错误会阻止发送DragExitedEvent。
解决方法是，可以在DragLeaveEvent中实现相关功能，该功能在停止拖放操作时发送。

## Making VisualElements draggable
为了使VisualElement可拖动，您需要在以下三种事件类型上注册回调。

### MouseDownEvent
当可拖动的VisualElement收到[MouseDownEvent](https://docs.unity3d.com/ScriptReference/UIElements.MouseDownEvent.html)时，需要将其状态设置为“准备拖动”。

### MouseUpEvent
当可拖动的VisualElement收到[MouseUpEvent](https://docs.unity3d.com/ScriptReference/UIElements.MouseUpEvent.html)时，需要重置其状态。

### MouseMoveEvent
当可拖动的VisualElement收到[MouseMoveEvent](https://docs.unity3d.com/ScriptReference/UIElements.MouseMoveEvent.html)并准备进行拖动时，它需要：
* 将其状态设置为“被拖动”。
* 将适当的数据添加到[DragAndDrop](https://docs.unity3d.com/ScriptReference/DragAndDrop.html)。
* 调用[DragAndDrop.StartDrag（）](https://docs.unity3d.com/ScriptReference/DragAndDrop.StartDrag.html)。
* 提供视觉反馈以表明它是拖动操作的对象。

放置区域VisualElement在收到`DragPerformEvent`或`DragExitedEvent`时应删除此反馈。