# Supported Events
事件系统支持许多事件，可以在用户自定义的用户编写的输入模块中进一步自定义事件。

独立输入模块和触摸输入模块支持的事件由接口提供，并且可以通过实现接口在MonoBehaviour上实现。
如果您配置了有效的事件系统，则会在正确的时间调用事件。
* [IPointerEnterHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerEnterHandler.html) - OnPointerEnter - Called when a pointer enters the object
* [IPointerExitHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerExitHandler.html) - OnPointerExit - Called when a pointer exits the object
* [IPointerDownHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerDownHandler.html) - OnPointerDown - Called when a pointer is pressed on the object
* [IPointerUpHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerUpHandler.html)- OnPointerUp - Called when a pointer is released (called on the GameObject that the pointer is clicking)
* [IPointerClickHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IPointerClickHandler.html) - OnPointerClick - Called when a pointer is pressed and released on the same object
* [IInitializePotentialDragHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IInitializePotentialDragHandler.html) - OnInitializePotentialDrag - Called when a drag target is found, can be used to initialize values
* [IBeginDragHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IBeginDragHandler.html) - OnBeginDrag - Called on the drag object when dragging is about to begin
* [IDragHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IDragHandler.html) - OnDrag - Called on the drag object when a drag is happening
* [IEndDragHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IEndDragHandler.html) - OnEndDrag - Called on the drag object when a drag finishes
* [IDropHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IDropHandler.html) - OnDrop - Called on the object where a drag finishes
* [IScrollHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IScrollHandler.html) - OnScroll - Called when a mouse wheel scrolls
* [IUpdateSelectedHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IUpdateSelectedHandler.html) - OnUpdateSelected - Called on the selected object each tick
* [ISelectHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.ISelectHandler.html) - OnSelect - Called when the object becomes the selected object
* [IDeselectHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IDeselectHandler.html) - OnDeselect - Called on the selected object becomes deselected
* [IMoveHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.IMoveHandler.html) - OnMove - Called when a move event occurs (left, right, up, down)
* [ISubmitHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.ISubmitHandler.html) - OnSubmit - Called when the submit button is pressed
* [ICancelHandler](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/api/UnityEngine.EventSystems.ICancelHandler.html) - OnCancel - Called when the cancel button is pressed