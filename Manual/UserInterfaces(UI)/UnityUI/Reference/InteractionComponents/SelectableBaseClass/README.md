# Selectable Base Class
Selectable类是所有交互组件的基类，它处理共同的项目。
|Property:|Function:
|:--------|:---------
|Interactable|这确定此组件是否将接受输入。 设置为false时，将禁用交互，并且过渡状态将设置为禁用状态。
|Transition|在一个可选组件中，有几个[Transition Options](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-SelectableTransition.html)，具体取决于该可选组件当前处于什么状态。不同的状态是： normal, highlighted, pressed 和 disabled。
|Navigation|还有许多[Navigation Options](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-SelectableNavigation.html)可控制如何实现控件的键盘导航。