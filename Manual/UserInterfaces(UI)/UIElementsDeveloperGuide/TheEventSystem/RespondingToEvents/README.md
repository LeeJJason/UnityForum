# Responding to Events
您可以通过两种方式使视觉元素对收到的事件做出反应：
* 通过注册事件回调，
* 通过实施默认操作。

您是否应该注册事件回调或实现默认操作取决于许多因素。

例如，如果实例化现有类中的对象，并且希望实例在接收事件时以特定的方式作出反应，则唯一的选择是在该实例上注册回调。

但是，如果从VisualElement派生一个新类，并且希望该类的所有实例以相同的方式对事件做出反应，则可以在构造函数中对该类的所有实例注册回调，也可以为该类实现默认操作。

回调和默认操作之间的根本区别如下：
* 回调必须在该类的实例上注册。默认操作在类上实现为虚函数。
* 对沿传播路径的所有元素执行回调。默认操作仅针对事件目标执行。

您可以通过调用`event.PreventDefault（）`来阻止执行默认操作。某些事件类型无法取消，这意味着它们的默认操作无法取消。您可以通过调用`event.StopPropagation（）`或`eventStopImmediatePropagation（）`来阻止执行回调，即使对于无法取消的事件也是如此。

您应该将默认操作视为特定类型的元素在收到事件时应具有的行为。

例如，复选框应通过切换其状态来响应click事件。应该通过重写默认操作虚拟函数而不是在所有复选框上注册回调来实现此行为。

通常，您应该更喜欢使用默认操作来实现元素自然期望的行为。这样可确保通过在附加到实例的回调中调用PreventDefault（）来逐个实例取消默认元素行为。

将行为实现为默认操作的其他好处是，它们的执行不需要在回调注册表中进行查找，并且对不带回调的实例进行了流/向上传播过程的优化。

为了获得更大的灵活性，可以在事件分发过程中的两个时刻执行事件目标的默认操作：
* 通过覆盖`ExecuteDefaultActionsAtTarget（）`，在目标回调执行后立即在down流传播和气泡传播阶段之间进行转换；
* 在事件分发过程的最后，通过重写`ExecuteDefaultActions（）`。

尽可能在`ExecuteDefaultActions（）`中实现您的类默认操作。这为类的用户提供了更多选项来覆盖它们：他们可以在事件传播过程的process流阶段或冒泡阶段中调用`PreventDefault（）`。

但是，有时候，您必须强制执行在类上执行默认操作的事件不会传播到元素的父级。例如，文本字段接收修改其值的按键事件。这些按键事件不会传播到父级，例如，可以使用Delete键删除内容。在这种情况下，请使用`ExecuteDefaultActionsAtTarget（）`实现默认操作，然后调用`StopPropagation（）`。这样可以确保在气泡上升阶段不会通过回调处理事件。

默认操作仅针对事件的目标执行。当元素由于事件传播过程而接收到事件时，将不执行它们。如果您希望班级对针对他们的孩子或父母的事件做出反应，则必须在班级的每个实例上注册一个回调。有时这是必要的，但是为了获得更好的可扩展性和更好的性能，请尝试避免在类的每个实例上注册回调。

## Registering an event callback
要将自定义行为添加到可视元素的特定实例，必须为触发该自定义行为的事件注册一个回调。

注册事件回调的优点在于，它使您可以自定义现有类的单个实例的行为。注册事件回调的缺点在于，由于执行时间较长，因此性能较低。执行需要更长的时间，因为无论何时接收到事件，都会检查已注册的事件以确定应执行哪个回调。

例如，以下代码为MouseDownEvent注册两个回调：
```
    // Register a callback on a mouse down event
    myElement.RegisterCallback<MouseDownEvent>(MyCallback);
    // Same, but also send some user data to the callback
    myElement.RegisterCallback<MouseDownEvent, MyType>(MyCallbackWithData, myData);
```

您的回调签名应如下所示：
```
void MyCallback(MouseDownEvent evt) { /* ... */ }
void MyCallbackWithData(MouseDownEvent evt, MyType data) { /* ... */ }
```

您可以根据需要注册任意数量的回调。但是，回调注册系统阻止您在给定的事件和传播阶段多次注册同一个回调函数。您可以通过调用myElement.UnregisterCallback（）方法从VisualElement中删除回调。

沿传播路径的每个元素（目标除外）两次接收事件：一次在流下降阶段，一次在冒泡阶段。为避免两次执行已注册的回调，请使用可选的RegisterCallback指定在哪个阶段执行回调。

默认情况下，在目标阶段和冒泡阶段期间执行注册的回调。此默认行为可确保父元素在其子元素之后做出反应。例如，如果您希望父母首先做出反应，以覆盖其子女的行为，则可以使用`TrickleDown.TrickleDown`选项注册回调：
```
    // Register a callback for the trickle down phase
    myElement.RegisterCallback<MouseDownEvent>(MyCallback, TrickleDown.TrickleDown);
```

这通知调度程序在目标阶段和流下降阶段执行回调。

## Implementing a default action
默认操作适用于该类的所有实例。这意味着对于元素接收的每个事件都调用一个或两个方法。

实现默认操作的类也可以在其实例上注册回调。

事件类实现在处理事件之前或之后执行的行为。事件类通过重写`EventBase`类的`PostDispatch（）`方法的`PreDispatch（）`来实现此目的。由于事件已排队，因此这些方法可以在处理事件而不是发出事件时更新状态或执行任务。例如，BlurEvent在处理该元素之前先更改当前焦点的元素。

实现默认操作要求派生`VisualElement`的新子类，并实现`ExecuteDefaultActionAtTarget（）`方法，`ExecuteDefaultAction（）`方法或两者。

默认动作是视觉元素子类的每个实例在收到事件时执行的动作。您可以通过覆盖`ExecuteDefaultActionAtTarget（）`和`ExecuteDefaultAction（）`来自定义默认操作：
```cs
override void ExecuteDefaultActionAtTarget(EventBase evt)
{
    // Do not forget to call the base function.
    base.ExecuteDefaultActionAtTarget(evt);

    if (evt.GetEventTypeId() == MouseDownEvent.TypeId())
    {
        // ...
    }
    else if (evt.GetEventTypeId() == MouseUpEvent.TypeId())
    {
        // ...
    }
    // More event types
}
```

为了获得更大的灵活性，您应该在`ExecuteDefaultAction（）`中实现默认操作。
如果需要，这允许用户停止或阻止执行默认操作。

如果希望目标默认操作在其父回调之前执行，请在`ExecuteDefaultActionAtTarget（）`中实现默认操作。

## The event handling sequence
当事件发生时，将沿着可视元素树中的传播路径发送事件。假设事件类型要求遵循事件传播的所有阶段，则将事件发送到每个可视元素。事件处理顺序如下：
* 对从根元素到事件目标的父元素的元素执行事件回调。这是调度过程的流阶段。
* 在事件目标上执行事件回调。这是调度过程的目标阶段。
* 调用目标ExecuteDefaultActionAtTarget（）。
* 对从事件目标父级到根的元素执行事件回调。这是调度过程的起泡沫阶段。
* 调用目标ExecuteDefaultAction（）。

当事件沿着传播路径发送时，Event.currentTarget被更新为当前处理该事件的元素。这意味着在事件回调函数中，Event.currentTarget是在其上注册回调的元素，而Event.target是在其上发生事件的元素。

### Stopping event propagation and cancelling default actions
您可以使用回调来停止，阻止和取消执行操作。但是，您不能阻止执行EventBase.PreDispatch（）和EventBase.PostDispatch（）方法。

以下方法影响事件传播和默认操作：
* `StopImmediatePropagation（）`立即停止事件传播过程。此事件不会执行其他回调。但是，仍然执行ExecuteDefaultActionAtTarget（）和ExecuteDefaultAction（）默认动作。
* StopPropagation（）在当前元素的最后一个回调之后停止事件传播过程。这样可以确保当前元素上的所有回调都已执行，但没有其他元素对该事件做出反应。 ExecuteDefaultActionAtTarget（）和ExecuteDefaultAction（）默认操作仍将执行。
* PreventDefaultAction（）防止调用ExecuteDefaultActionAtTarget（）和ExecuteDefaultAction（）默认操作。 PreventDefaultAction（）不会阻止其他回调被执行。另外，如果您在冒泡阶段中调用PreventDefaultAction（），则不会阻止ExecuteDefaultActionAtTarget（）默认操作，因为它已被执行。 ExecuteDefaultActionAtTarget（）默认操作在流向下阶段执行。