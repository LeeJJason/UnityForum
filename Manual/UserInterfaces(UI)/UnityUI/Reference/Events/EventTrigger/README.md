# [Event Trigger](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/script-EventTrigger.html)

事件触发器从事件系统接收事件，并为每个事件调用已注册的函数。

事件触发器可用于指定希望为每个事件系统事件调用的函数。 您可以将多个功能分配给单个事件，并且一旦事件触发器接收到该事件，它将调用这些功能。

请注意，将事件触发器组件附加到GameObject将使该对象拦截所有事件，并且该对象不会发生任何事件冒泡！

## Events
通过单击“添加新事件类型”按钮，可以选择将每个[**Supported Events**](https://docs.unity3d.com/Packages/com.unity.ugui@1.0/manual/SupportedEvents.html)包括在事件触发器中。