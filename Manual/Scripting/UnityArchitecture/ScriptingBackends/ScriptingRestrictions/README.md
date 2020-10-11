# Scripting restrictions
Unity努力在其支持的所有平台上提供通用的脚本API和体验。但是，某些平台具有固有的限制。为了帮助您理解这些限制并支持跨平台代码，下表描述了哪些限制适用于每个平台和脚本后端：

## .NET 4.x equivalent scripting runtime

| Platform (scripting backend) | Ahead-of-time compile |No threads | .NET Core class libraries subset
|:------|:-------|:----|:-----
|Android (IL2CPP)|✔	|	|
|Android (Mono)	|	|	|
|iOS (IL2CPP) |	✔	||
|PlayStation 4 (IL2CPP)|	✔|||		
|PlayStation Vita (IL2CPP)	|✔|||		
|Standalone (IL2CPP)|	✔|||		
|Standalone (Mono)	|	|	|
|Switch (IL2CPP)|	✔|||		
|Universal Windows Platform (IL2CPP)|	✔|||
|Universal Windows Platform (.NET)	|	|	|✔
|WebGL (IL2CPP)|	✔|	✔	||
|WiiU (Mono)	|	|	|
|XBox One (IL2CPP)|	✔	|	

## Ahead-of-time compile
某些平台不允许运行时代码生成。因此，任何依赖于目标设备上的即时（JIT）编译的托管代码都将失败。相反，您需要提前编译所有托管代码（AOT）。通常，这种区别并不重要，但是在某些特定情况下，需要进一步考虑AOT平台。

### System.Reflection.Emit
AOT平台无法实现**System.Reflection.Emit**命名空间中的任何方法。只要编译器可以推断出通过反射使用的代码需要在运行时存在，其余的**System.Reflection**都是可以接受的。

### Serialization
由于使用反射，AOT平台可能会遇到序列化和反序列化的问题。如果仅通过反射将类型或方法用作序列化或反序列化的一部分，则AOT编译器无法检测到需要为该类型或方法生成代码。

### Generic virtual methods
通用方法要求编译器做一些额外的工作，以将开发人员编写的代码扩展为在设备上执行的代码。例如，您需要使用int或double的List的不同代码。在存在虚拟方法的情况下，行为是在运行时而不是在编译时确定的，因此编译器可以轻松地要求在源代码不完全明显的地方生成运行时代码。

以下代码示例在JIT平台上完全按预期工作（它将“消息值：零”打印到控制台一次）：
```cs
public class AOTProblemExample : MonoBehaviour, IReceiver
{
    public enum AnyEnum 
    {
        Zero,
        One,
    }

    void Start() 
    {
        // Subtle trigger: The type of manager *must* be
        // IManager, not Manager, to trigger the AOT problem.
        IManager manager = new Manager();
        manager.SendMessage(this, AnyEnum.Zero);
    }

    public void OnMessage<T>(T value) 
    {
        Debug.LogFormat("Message value: {0}", value);
    }
}

public class Manager : IManager 
{
    public void SendMessage<T>(IReceiver target, T value) {
        target.OnMessage(value);
    }
}

public interface IReceiver
{
    void OnMessage<T>(T value);
}

public interface IManager 
{
    void SendMessage<T>(IReceiver target, T value);
}
```
但是，当您使用IL2CPP在AOT平台上执行此代码时脚本后端，发生此异常：
```cs
ExecutionEngineException: Attempting to call method 'AOTProblemExample::OnMessage<AOTProblemExample+AnyEnum>' for which no ahead of time (AOT) code was generated.
  at Manager.SendMessage[T] (IReceiver target, .T value) [0x00000] in <filename unknown>:0 
  at AOTProblemExample.Start () [0x00000] in <filename unknown>:0 
```
同样，Mono脚本后端提供了类似的例外：
```cs
ExecutionEngineException: Attempting to JIT compile method 'Manager:SendMessage<AOTProblemExample/AnyEnum> (IReceiver,AOTProblemExample/AnyEnum)' while running with --aot-only.
  at AOTProblemExample.Start () [0x00000] in <filename unknown>:0 
```
AOT编译器没有意识到它应该为T为AnyEnum的通用方法OnMessage生成代码，因此它继续执行，跳过了该方法。
当调用该方法，并且运行时找不到合适的代码来执行时，它将返回此错误消息。

若要解决这样的AOT问题，您可以强制编译器生成正确的代码。您可以将以下示例方法添加到AOTProblemExample类：
```cs
public void UsedOnlyForAOTCodeGeneration() 
{
    // IL2CPP needs only this line.
    OnMessage(AnyEnum.Zero);

    // Mono also needs this line. Note that we are
    // calling directly on the Manager, not the IManager interface.
    new Manager().SendMessage(null, AnyEnum.Zero);

    // Include an exception so we can be sure to know if this method is ever called.
    throw new InvalidOperationException("This method is used for AOT code generation only. Do not call it at runtime.");
}
```

当编译器遇到带有AnyEnum T的对OnMessage的显式调用时，它将生成适当的代码供运行时执行。无需调用UsedOnlyForAOTCodeGeneration方法；它只需要存在，编译器就可以看到它。

### Calling managed methods from native code
需要编组到C函数指针以便可以从本机代码调用的托管方法在AOT平台上有一些重构：
* 托管方法必须是静态方法
* 托管方法必须具有[MonoPInvokeCallback]属性

## No threads
某些平台不支持使用线程，因此任何使用System.Threading命名空间的托管代码都将在运行时失败。此外，.NET类库的某些部分隐式依赖于线程。一个经常使用的示例是System.Timers.Timer类，它取决于对线程的支持。

## Exception filters
IL2CPP不支持C＃异常过滤器。您应该将依赖于异常过滤器的代码修改为适当的catch块。

## TypedReference
IL2CPP不支持**System.TypedReference**类型或**__makeref** C＃关键字。