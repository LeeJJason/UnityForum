## Overview of .NET in Unity
Unity使用开源.NET平台，以确保您使用Unity制作的应用程序可以在各种不同的硬件配置上运行。 .NET支持多种语言和API库。

### Scripting backends
Unity有两个脚本后端，**Mono**和[IL2CPP](https://docs.unity3d.com/Manual/IL2CPP.html)（C++的中间语言），每种都使用不同的编译技术：
* Mono使用即时（JIT）编译，并在运行时按需编译代码。
* IL2CPP使用提前（AOT）编译并在运行之前编译整个应用程序。

使用基于JIT的脚本后端的好处是，编译时间通常比AOT快得多，并且与平台无关。

Unity Editor基于JIT，并使用Mono作为脚本后端。为应用程序构建播放器时，可以选择要使用的脚本后端。要通过编辑器执行此操作，请转到**Edit > Project Settings > Player**，打开**Other Settings**面板，然后单击**Scripting Backend**下拉列表并选择所需的后端。

### Managed code stripping
生成应用程序时，Unity会扫描已编译的程序集（.DLL），以检测并删除未使用的代码。此过程减小了构建的最终二进制文件大小，但增加了构建时间。  
当您使用Mono时，默认情况下将禁用代码剥离，但是IL2CPP不能禁用代码剥离。您可以控制剥离代码时Unity的攻击性。转到**Edit > Project Settings > Player**，打开**Other Settings**面板，然后单击**Managed Stripping Level**下拉菜单，然后选择所需的代码剥离级别。有关代码剥离的更多信息，请参见[托管代码剥离文档](https://docs.unity3d.com/Manual/ManagedCodeStripping.html)。

**注意**：在某些情况下，代码剥离可能过于激进，并且可能会删除您依赖的代码，尤其是在使用反射时。您可以使用**Preserve**属性和link.xml文件来防止剥离特定的类型和功能。

### Garbage collection
Unity将[Boehm garbage collector](https://en.wikipedia.org/wiki/Boehm_garbage_collector)用于Mono和IL2CPP后端。默认情况下，Unity使用**增量**模式。您可以禁用**增量**模式以使用**stop the world**垃圾收集，尽管Unity建议使用增量模式。  
要在增量模式和**stop the world**之间切换，请转到**Edit > Project Settings > Player**，打开**Other Settings**面板，然后单击**Use incremental GC**复选框。在增量模式下，Unity的垃圾收集器仅运行有限的一段时间，并不一定要一次性收集所有对象。这样可以将收集对象的时间分散到多个帧上，并减少了卡顿现象和CPU峰值。
有关更多信息，请参见了解[自动内存管理](https://docs.unity3d.com/Manual/UnderstandingAutomaticMemoryManagement.html)。  
要检查应用程序中的分配数量和可能的CPU峰值，请使用Unity Profiler。您还可以使用GarbageCollector API完全禁用播放器中的垃圾收集。禁用收集器时，应小心避免分配过多的内存。

### .NET system libraries
Unity支持许多平台，并可能根据平台使用不同的脚本后端。 .NET系统库需要特定于平台的实现才能在某些情况下正常工作。尽管Unity尽最大努力支持尽可能多的.NET生态系统，但是.NET系统库的某些部分存在一些例外，而Unity明确不支持这些库。

Unity不对跨Unity版本的.NET系统库提供性能或分配保证。根据一般经验，Unity不会在.NET系统库中修复任何性能下降问题。  
Unity不支持System.Drawing库，因此不能保证在所有平台上都能工作。  
JIT脚本后端允许您在应用程序运行时发出动态C＃/。NET中间语言（IL）代码生成，而AOT脚本后端不支持动态代码生成。使用第三方库时，必须考虑这一点，因为它们对于JIT和AOT可能具有不同的代码路径，或者它们可能使用依赖于动态生成的代码的代码路径。有关如何在运行时生成代码的更多信息，请参见Microsoft的ModuleBuilder文档。  

尽管Unity支持多个.NET API配置文件，但出于以下原因，您应将.NET Standard 2.0 API兼容性级别用于所有新项目：
* .NET Standard 2.0是一个较小的API表面，因此具有较小的实现。这样可以减小最终可执行文件的大小。
* .NET Standard 2.0具有更好的跨平台支持，因此您的代码更有可能在所有平台上工作。所有 .NET运行时都支持.NET Standard 2.0，因此您的代码可在更多VM /运行时环境（例如 .NET Framework，.NET Core，Xamarin，Unity）中运行。
* .NET Standard将更多错误移至编译时。 
* .NET 4.7.1中有许多API在编译时可用，但某些平台上的实现会在运行时引发异常。

例如，如果您需要为较旧的现有应用程序提供支持，则其他配置文件可能很有用。如果需要不同的API兼容性级别，请在“播放器设置”中更改 **.NET Profile**。为此，请转到**Edit > Project Settings > Player > Other Settings**，然后从**Api Compatibility Level**下拉列表中选择所需的级别。

### Using third-party .NET libraries
您只应使用在广泛的Unity配置和平台上经过广泛测试的第三方.NET库。  
**注意**：第三方库中的JIT和AOT代码路径的性能特征可能会大大不同。由于这个原因，AOT通常会减少启动时间，并且适合于较大的应用程序，但是会增加二进制文件的大小以容纳已编译的代码。在开发期间，AOT的构建时间也更长，并且无法更改已编译代码的行为以针对任何一种特定平台。 JIT在运行时会根据其运行的平台进行调整，从而可以提高运行性能，但可能会延长应用程序的启动时间。因此，您应该在编辑器和目标平台上都对应用程序进行概要分析。
有关更多信息，请参见Unity Profiler文档。  

您应该分析.NET系统库在所有目标平台上的使用情况，因为它们的性能特征可能会根据所使用的脚本后端，.NET版本和配置文件而有所不同。

当您查看第三方库时，请考虑以下方面：
* 兼容性：第三方库可能与某些Unity平台和脚本后端不兼容。
* 性能：与其他.NET运行时相比，第三方库在Unity中可能具有截然不同的性能特征。
* AOT二进制大小：第三方库可能会由于依赖项的数量而大大增加AOT二进制大小图书馆使用。

### C# reflection overhead
Mono和IL2CPP在内部缓存所有C＃反射（System.Reflection）对象，根据设计，Unity不会对它们进行垃圾回收。
此行为的结果是，垃圾收集器在应用程序的生命周期内连续扫描缓存的C＃反射对象，这将导致不必要的垃圾回收，并可能会导致大量的垃圾回收开销。  

为了最大程度地减少垃圾收集器的开销，请避免在应用程序中使用Assembly.GetTypes和Type.GetMethods（）之类的方法，这些方法在运行时会创建许多C＃反射对象。相反，您应该在编辑器中扫描程序集以获取所需的数据，并对其进行序列化和/或编码生成以在运行时使用。

### UnityEngine.Object special behavior
UnityEngine.Object是Unity中C＃对象的一种特殊类型，因为它链接到本机C ++对应对象。例如，当您使用相机时
组件，Unity不会将对象的状态存储在C＃对象上，而是存储在其本机C ++副本中。  
![](unity-engine-object.png)  

---

Unity当前不支持将C＃WeakReference类与UnityEngine.Objects一起使用。因此，您不应使用WeakReference引用已加载的资产。有关WeakReference类的更多信息，请参见Microsoft的WeakReference文档。

### Unity C# and Unity C++ share UnityEngine Objects
当您使用诸如Object.Destroy或Object.DestroyImmediate之类的方法销毁UnityEngine.Object派生的对象时，Unity销毁（卸载）本机计数器对象。您不能通过显式调用销毁C＃对象，因为垃圾回收器管理内存。
一旦不再有对托管对象的引用，垃圾收集器将收集并销毁它。  

如果再次访问已破坏的UnityEngine.Object，则Unity会为大多数类型重新创建本机对应对象。这种重新创建行为的两个例外是MonoBehaviour和ScriptableObject：一旦销毁它们，Unity就不会重新加载它们。  

MonoBehaviour和ScriptableObject覆盖等号（==）和不等号（！=）运算符。因此，如果将销毁的MonoBehaviour或ScriptableObject与null进行比较，则当托管对象仍然存在并且尚未进行垃圾回收时，运算符将返回true。

因为??和？运算符不可重载，它们与从UnityEngine.Object派生的对象不兼容。在托管对象仍然存在的情况下，在销毁的MonoBehaviour或ScriptableObject上使用运算符时，运算符不会返回与相等运算符和不相等运算符相同的结果。

### Avoid using async and await
Unity API不是线程安全的，因此，您不应使用异步和等待任务。异步任务通常在调用时分配对象，如果您过度使用它们，可能会导致性能问题。此外，退出播放模式时，Unity不会自动停止在托管线程上运行的异步任务。

Unity使用自定义UnitySynchronizationContext覆盖默认的SynchronizationContext，并在“编辑”和“播放”模式下在主线程上运行所有任务。要利用异步任务，您必须使用TaskFactory手动创建和处理自己的线程，并使用默认的SynchronizationContext而不是Unity版本。要侦听进入和退出播放模式事件以手动停止任务，请使用EditorApplication.playModeStateChanged。但是，如果您采用这种方法，则大多数Unity脚本API均无法使用，因为您没有使用UnitySynchronizationContext。