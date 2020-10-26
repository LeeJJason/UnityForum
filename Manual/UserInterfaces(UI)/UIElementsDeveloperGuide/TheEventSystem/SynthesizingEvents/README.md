# Synthesizing Events
在合成和发送自定义事件之前，您应该了解UIElement如何分配和发送操作系统事件。

UIElements使用事件池来避免重复分配事件对象。要合成和发送自己的事件，应按照以下步骤分配和发送事件：
1. 从事件池中获取事件对象。
2. 填写事件属性。
3. 将事件包含在using块中，以确保将其返回到事件池。
4. 将事件传递给element.SendEvent（）。

如果要发送通常来自操作系统的事件，例如键盘事件和某些鼠标事件，请使用UnityEngine.Event初始化UIElements事件。

下面的示例演示如何合成和发送事件：
```cs
void SynthesizeAndSendKeyDownEvent(IPanel panel, KeyCode code,
     char character = '\0', EventModifiers modifiers = EventModifiers.None)
{
    // Create a UnityEngine.Event to hold initialization data.
    // Also, this event will be forwarded to IMGUIContainer.m_OnGUIHandler
    var evt = new Event() {
        type = EventType.KeyDownEvent,
        keyCode = code,
        character = character,
        modifiers = modifiers
    };

    using (KeyDownEvent keyDownEvent = KeyDownEvent.GetPooled(evt))
    {
        panel.SendEvent(keyDownEvent);
    }
}
```