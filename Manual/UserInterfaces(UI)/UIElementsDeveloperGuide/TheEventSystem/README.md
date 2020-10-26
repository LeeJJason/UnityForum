# The Event System
UIElements包含一个事件系统将用户互动传达给视觉元素。受HTML事件启发，UIElements事件系统共享许多相同的术语和事件命名。 UlElement事件系统包括以下内容：
* Event dispatcher: UIElements侦听来自操作系统或脚本的事件，并使用[Event dispatcher](https://docs.unity3d.com/Manual/UIE-Events-Dispatching.html)分配这些事件。事件分配器还确定用于将事件发送到视觉元素和其他支持类的分配策略。
* Event handler：当面板内发生事件时，该事件将发送到面板内的VisualElement树。您可以将事件处理程序添加到可视元素，以在某些事件类型发生时做出响应。请参[阅响应事件](https://docs.unity3d.com/Manual/UIE-Events-Handling.html)。
* Event synthesizer: 操作系统不是唯一的事件源。脚本还可以通过调度程序创建和调度事件。有关创建和调度事件的更多信息，请参见[Synthesizing Events](https://docs.unity3d.com/Manual/UIE-Events-Synthesizing.html)。
* Event types: 不同的事件类型基于EventBase被组织到层次结构中，并分为族。每个事件家族都实现一个接口，该接口定义同一家族的所有事件的共同特征。例如，MouseUpEvent，MouseDownEvent和其他鼠标事件实现IMouseEvent接口。此接口指定每个鼠标事件都有一个位置，一个按下的按钮，一组修饰符以及其他与鼠标相关的事件类型。有关每个事件系列及其UIElement事件类型的描述，请参见[事件类型参考](https://docs.unity3d.com/Manual/UIE-Events-Reference.html)。

您还可以使用事件将其他类型的消息传达给可视元素。例如，ContextualMenuManager使用ContextualMenuPopulateEvent将项目添加到上下文菜单。请参阅[内置控件](https://docs.unity3d.com/Manual/UIE-Controls.html)。