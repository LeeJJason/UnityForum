# NativeContainer
[安全系统](https://docs.unity3d.com/Manual/JobSystemSafetySystem.html)复制数据过程的缺点在于，它还会隔离每个副本中的作业结果。要克服此限制，您需要将结果存储在一种称为[NativeContainer](https://docs.unity3d.com/ScriptReference/Unity.Collections.LowLevel.Unsafe.NativeContainerAttribute.html)的共享内存中。

## What is a NativeContainer?
**NativeContainer**是一种托管值类型，可为本地内存提供相对安全的C＃包装器。它包含一个指向非托管分配的指针。与Unity C# Job System一起使用时，**NativeContainer**允许作业访问与主线程共享的数据，而不是使用副本。

## What types of NativeContainer are available?
Unity附带了一个称为[NativeArray](https://docs.unity3d.com/ScriptReference/Unity.Collections.NativeArray_1.html)的**NativeContainer**。您还可以使用[NativeSlice](https://docs.unity3d.com/ScriptReference/Unity.Collections.NativeSlice_1.html)操作**NativeArray**，以从特定位置到特定长度获取NativeArray的子​​集。

注意：[实体组件系统（ECS）](https://github.com/Unity-Technologies/EntityComponentSystemSamples)包扩展了**Unity.Collections**命名空间，以包括其他类型的**NativeContainer**：
* NativeList - a resizable NativeArray.
* NativeHashMap - key and value pairs.
* NativeMultiHashMap - multiple values per key.
* NativeQueue - a first in, first out (FIFO) queue.

## NativeContainer and the safety system
该安全系统内置于所有本机容器类型中。它跟踪正在读取和写入任何本机容器的内容。

注意：关于NativeContainer类型的所有安全检查（例如越界检查，解除分配检查和竞争条件检查）仅在Unity编辑器和播放模式下可用。

此安全系统的一部分是[DisposeSentinel](https://docs.unity3d.com/ScriptReference/Unity.Collections.LowLevel.Unsafe.DisposeSentinel.html)和[AtomicSafetyHandle](https://docs.unity3d.com/ScriptReference/Unity.Collections.LowLevel.Unsafe.AtomicSafetyHandle.html)。如果未正确释放内存，DisposeSentinel会检测到内存泄漏并给您一个错误。触发内存泄漏错误发生在泄漏发生很长时间之后。

使用AtomicSafetyHandle在代码中转移NativeContainer的所有权。例如，如果两个计划的作业正在写入同一NativeArray，则安全系统将引发异常，并显示一条清晰的错误消息，以解释原因以及如何解决该问题。当您安排有问题的作业时，安全系统将引发此异常。

在这种情况下，您可以安排具有依赖关系的作业。第一个作业可以写入NativeContainer，一旦完成执行，下一个作业就可以安全地读取和写入相同的NativeContainer。从主线程访问数据时，读取和写入限制也适用。安全系统确实允许多个作业并行地从同一数据读取。

默认情况下，当作业可以访问NativeContainer时，它同时具有读取和写入访问权限。此配置可能会降低性能。 C＃作业系统不允许您安排一个具有对NativeContainer的写访问权限的作业与另一个正在写入该作业的作业。

如果作业不需要写入NativeContainer，请使用[ReadOnly]属性标记NativeContainer，如下所示：
```cs
[ReadOnly]
public NativeArray<int> input;
```

在上面的示例中，您可以与对第一个NativeArray具有只读访问权限的其他作业同时执行该作业。

注意：无法防止从作业内部访问静态数据。访问静态数据会绕过所有安全系统，并可能使Unity崩溃。有关更多信息，请参见[C＃作业系统提示和故障排除](https://docs.unity3d.com/Manual/JobSystemTroubleshooting.html)。

## NativeContainer Allocator
创建NativeContainer时，必须指定所需的内存分配类型。分配类型取决于作业运行的时间长度。这样，您可以调整分配以在每种情况下获得最佳性能。

有三种用于NativeContainer内存分配和释放的[分配器](https://docs.unity3d.com/ScriptReference/Unity.Collections.Allocator.html)类型。在实例化NativeContainer时需要指定适当的一个。
* **Allocator.Temp** 分配最快。它用于寿命不超过一帧的分配。您不应将使用Temp的NativeContainer分配传递给作业。在从方法调用返回之前，还需要调用Dispose方法（例如MonoBehaviour.Update或从本机代码到托管代码的任何其他回调）。
* **Allocator.TempJob** 是比Temp慢的分配，但比Persistent快。它用于在四个帧的生命周期内进行分配，并且是线程安全的。如果您没有在四帧之内将其丢弃，则控制台会打印一条从本机代码生成的警告。大多数小型作业都使用此NativeContainer分配类型。
* **Allocator.Persistent** 是最慢的分配，但是可以根据需要持续使用，并且在必要时可以在应用程序的整个生命周期内持续使用。它是直接调用malloc的包装。较长的作业可以使用此NativeContainer分配类型。在性能至关重要的情况下，您不应使用**Persistent**。

例如：
```cs
NativeArray<float> result = new NativeArray<float>(1, Allocator.TempJob);
```
注意：上例中的数字1表示NativeArray的大小。在这种情况下，它只有一个数组元素（因为它只存储结果中的一个数据）。