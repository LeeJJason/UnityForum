# C# Job System tips and troubleshooting
使用Unity C＃作业系统时，请确保遵守以下规定：

## Do not access static data from a job
从工作中访问静态数据会绕过所有安全系统。如果您访问了错误的数据，则可能会导致Unity崩溃，并且经常发生意外情况。例如，访问MonoBehaviour可能会导致域重新加载时崩溃。

注意：由于存在这种风险，未来的Unity版本将阻止使用静态分析从作业中访问全局变量。如果您确实要访问作业中的静态数据，则应该预期代码会在Unity的未来版本中中断。

## Flush scheduled batches
当您希望作业开始执行时，可以使用JobHandle.ScheduleBatchedJobs刷新计划的批处理。请注意，调用此方法可能会对性能产生负面影响。不刷新批处理将延迟调度，直到主线程等待结果为止。在所有其他情况下，请使用JobHandle.Complete启动执行过程。

注意：在实体组件系统（ECS）中，该批次将为您隐式刷新，因此无需调用JobHandle.ScheduleBatchedJobs。

## Don’t try to update NativeContainer contents
由于缺少引用返回，因此无法直接更改NativeContainer的内容。例如，`nativeArray [0] ++`;与编写`var temp = nativeArray[0]; temp++`相同；这不会更新**nativeArray**中的值。

相反，您必须将数据从索引复制到本地临时副本，修改该副本，然后将其保存回，如下所示：
```cs
MyStruct temp = myNativeArray[i];
temp.memberVariable = 0;
myNativeArray[i] = temp;
```

## Call JobHandle.Complete to regain ownership
跟踪数据所有权需要依赖项在主线程可以再次使用它们之前完成。仅检查JobHandle.IsCompleted是不够的。您必须调用方法JobHandle.Complete才能将NativeContainer类型的所有权重新获得到主线程。调用完成还会清除安全系统中的状态。不这样做会导致内存泄漏。如果您在每个依赖于前一帧作业的帧上计划新作业，则此过程也适用。

## Use Schedule and Complete in the main thread
您只能从主线程调用Schedule和Complete。如果一个作业依赖于另一个作业，请使用JobHandle来管理依赖关系，而不要尝试在作业中安排作业。

## Use Schedule and Complete at the right time
拥有所需的数据后，立即致电计划表，在需要结果之前不要致电完成表。优良作法是安排一个不需要与正在运行的其他作业竞争的不需要等待的作业。例如，如果您在一个帧的结束到下一帧的开始之间有一个不运行任何作业的时间段，并且可以接受一帧的延迟，则可以将作业调度到一帧的结尾并使用其结果在以下框架中。或者，如果您的游戏使该转换期与其他作业饱和，并且框架中其他地方存在未充分利用的较大时期，那么将作业安排在那里的效率更高。

## Mark NativeContainer types as read-only
请记住，默认情况下，作业具有对NativeContainer类型的读写权限。适当时使用[ReadOnly]属性可提高性能。

## Check for data dependencies
在Unity Profiler中在窗口中，主线程上的标记“ WaitForJobGroup”指示Unity正在等待工作线程上的作业完成。此标记可能意味着您已在应解决的地方引入了数据依赖性。查找JobHandle.Complete以跟踪您在哪些数据依赖项中强迫主线程等待。

## Debugging jobs
作业具有“运行”功能，您可以使用它代替Schedule来立即在主线程上执行作业。您可以将其用于调试目的。

## Do not allocate managed memory in jobs
在作业中分配托管内存的速度非常慢，并且该作业无法利用Unity Burst编译器来提高性能。 Burst是一项新的基于LLVM的后端编译器技术，使您的工作更轻松。它利用C＃作业并利用平台的特定功能来生成高度优化的机器代码。

## Further information
* 观看[Unity GDC 2018：C＃Job System演讲清单](https://www.youtube.com/playlist?list=PLX2vGYjWbI0RuXtGMYKqChoZC2b-H4tck)。
* 有关C＃作业系统如何与ECS相关的更多高级信息，请参阅[ECS软件包文档](https://docs.unity3d.com/Packages/com.unity.entities@latest?preview=1&subfolder=/manual/index.html)。
* 有关进一步的讨论和故障排除，请参阅[面向数据的技术堆栈论坛](https://forum.unity.com/forums/data-oriented-technology-stack.147/?_ga=2.110546892.814652733.1602412444-1096585399.1561227707)。