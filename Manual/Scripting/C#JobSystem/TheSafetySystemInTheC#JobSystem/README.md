# The safety system in the C# Job System

## Race conditions
在编写[多线程代码](https://docs.unity3d.com/Manual/JobSystemMultithreading.html)时，总是存在[竞争条件](https://en.wikipedia.org/wiki/Race_condition)的风险。当一个操作的输出取决于其控制之外的另一个进程的时间时，就会发生竞争状态。

竞争条件并不总是错误，而是不确定行为的来源。当竞争条件确实导致错误时，可能很难找到问题的根源，因为它取决于时间，因此您只能在极少数情况下重现问题。对其进行调试可能会导致问题消失，因为断点和日志记录可能会更改单个线程的时间。竞争条件在编写多线程代码方面带来了最大的挑战。

## Safety system
为了使编写多线程代码更容易，Unity C# Job System会检测所有潜在的竞争情况，并保护您免受可能引起的错误的影响。

例如：如果C# Job System将对数据的引用从主线程中的代码发送到作业，则它无法验证主线程是否在作业写入的同时正在读取数据。这种情况下创建竞争条件。

C# Job System 通过向每个作业发送它需要对其进行操作的数据的副本而不是对主线程中的数据的引用来解决此问题。此副本隔离了数据，从而消除了竞争状况。

C# Job System 复制数据的方式意味着作业只能访问可 blittable types。在托管代码和本机代码之间传递时，这些类型不需要转换。

C# Job System 可以使用memcpy复制 blittable types，并在Unity的托管部分和本机部分之间传输数据。在 Scheduling jobs 时，它使用memcpy将数据放入本机内存中，并在执行作业时使被管理方访问该副本。有关更多信息，请参阅[Scheduling jobs](https://docs.unity3d.com/Manual/JobSystemSchedulingJobs.html)。