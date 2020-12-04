# Messaging System
新的UI系统使用了一个消息传递系统来代替SendMessage。该系统是纯C的，旨在解决SendMessage中存在的一些问题。系统使用可在monobhavior上实现的自定义接口来指示组件能够从消息传递系统接收回调。当进行调用时，将指定一个目标游戏对象；该调用将在实现指定接口的游戏对象的所有组件上发出。消息传递系统允许传递自定义用户数据，以及事件应该在GameObject层次结构中传播多远；也就是说，它应该只针对指定的游戏对象执行，还是也应该在子级和父级上执行。除此之外，消息传递框架还提供帮助函数来搜索和查找实现给定消息传递接口的游戏对象。

消息传递系统是通用的，不仅为UI系统设计，也为一般游戏代码使用而设计。添加自定义消息传递事件相对来说比较简单，它们将使用UI系统用于所有事件处理的相同框架来工作。

## Defining A Custom Message
如果希望定义自定义消息，则相对简单。在UnityEngine.EventSystems命名空间有一个名为“IEventSystemHandler”的基接口。任何由此扩展的内容都可以被视为通过消息传递系统接收事件的目标。
```cs
public interface ICustomMessageTarget : IEventSystemHandler
{
    // functions that can be called via the messaging system
    void Message1();
    void Message2();
}
```

一旦定义了这个接口，就可以用MonoBehaviour来实现它。当实现时，它定义了当给定消息针对这个MonoBehaviour游戏对象发出时将执行的函数。
```cs
public class CustomMessageTarget : MonoBehaviour, ICustomMessageTarget
{
    public void Message1()
    {
        Debug.Log ("Message 1 received");
    }

    public void Message2()
    {
        Debug.Log ("Message 2 received");
    }
}
```

现在已经存在可以接收消息的脚本，我们需要发出消息。通常，这是对某些松散耦合事件的响应。例如，在UI系统中，我们为诸如P​​ointerEnter和PointerExit之类的事件发出事件，以及响应于用户输入到应用程序中而可能发生的各种其他事件。

要发送消息，存在一个静态助手类来执行此操作。作为参数，它需要消息的目标对象，一些用户特定的数据以及映射到您希望目标的消息接口中的特定功能的函子。
```cs
ExecuteEvents.Execute<ICustomMessageTarget>(target, null, (x,y)=>x.Message1());
```

此代码将在GameObject目标上实现ICustomMessageTarget接口的任何组件上执行Message1函数。 ExecuteEvents类的脚本文档涵盖了Execute函数的其他形式，例如在孩子或父母中执行。