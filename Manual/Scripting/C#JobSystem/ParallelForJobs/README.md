# ParallelFor jobs
[安排作业](https://docs.unity3d.com/Manual/JobSystemSchedulingJobs.html)时，只能有一项作业完成一项任务。在游戏中，通常要对大量对象执行相同的操作。有一个单独的作业类型称为[IJobParallelFor](https://docs.unity3d.com/ScriptReference/Unity.Jobs.IJobParallelFor.html)来处理此问题。

注意：“ ParallelFor”作业是Unity中所有实现**IJobParallelFor**接口的结构的统称。

ParallelFor作业使用[NativeArray](https://docs.unity3d.com/ScriptReference/Unity.Collections.NativeArray_1.html)数据作为其数据源。 ParallelFor作业跨多个核心运行。每个核心有一个作业，每个作业处理一部分工作量。 IJobParallelFor的行为类似于IJob，但它不是单个Execute方法，而是对数据源中的每个项目调用一次[Execute](https://docs.unity3d.com/ScriptReference/Unity.Jobs.IJob.Execute.html)方法。在Execute方法中有一个整数参数。该索引用于访问作业实现中的数据源的单个元素并对其进行操作。

## An example of a ParallelFor job definition:
```cs
struct IncrementByDeltaTimeJob: IJobParallelFor
{
    public NativeArray<float> values;
    public float deltaTime;

    public void Execute (int index)
    {
        float temp = values[index];
        temp += deltaTime;
        values[index] = temp;
    }
}
```

## Scheduling ParallelFor jobs
调度ParallelFor作业时，必须指定要拆分的NativeArray数据源的长度。如果结构中有多个，则Unity C＃作业系统无法知道您要将哪个NativeArray用作数据源。该长度还告诉C＃作业系统需要多少个Execute方法。

幕后花絮，ParallelFor作业的调度更加复杂。调度ParallelFor作业时，C＃作业系统将作业分为几批以在内核之间分配。每个批处理都包含Execute方法的子集。然后，C＃作业系统在每个CPU内核的Unity本地作业系统中最多调度一个作业，并将该本地作业分批传递完成。  
![](jobsystem_parallelfor_job_batches.svg)  
*A ParallelFor job dividing batches across cores*

当本机作业比其他作业先完成其批次时，它将从其他本机作业中窃取剩余的批次。它一次只能窃取本机作业剩余批次的一半，以确保缓存位置。

要优化流程，您需要指定批次计数。批计数控制您获得多少作业，以及线程之间的工作重新分配的粒度。批处理计数较低（例如1）可以使线程之间的工作分配更加均匀。它的确有一些开销，因此有时最好增加批数。从1开始并增加批数直到出现微不足道的性能提升是一种有效的策略。

## An example of scheduling a ParallelFor job

**Job code:**
```cs
// Job adding two floating point values together
public struct MyParallelJob : IJobParallelFor
{
    [ReadOnly]
    public NativeArray<float> a;
    [ReadOnly]
    public NativeArray<float> b;
    public NativeArray<float> result;

    public void Execute(int i)
    {
        result[i] = a[i] + b[i];
    }
}
```

**Main thread code:**
```cs
NativeArray<float> a = new NativeArray<float>(2, Allocator.TempJob);

NativeArray<float> b = new NativeArray<float>(2, Allocator.TempJob);

NativeArray<float> result = new NativeArray<float>(2, Allocator.TempJob);

a[0] = 1.1;
b[0] = 2.2;
a[1] = 3.3;
b[1] = 4.4;

MyParallelJob jobData = new MyParallelJob();
jobData.a = a;  
jobData.b = b;
jobData.result = result;

// Schedule the job with one Execute per index in the results array and only 1 item per processing batch
JobHandle handle = jobData.Schedule(result.Length, 1);

// Wait for the job to complete
handle.Complete();

// Free the memory allocated by the arrays
a.Dispose();
b.Dispose();
result.Dispose();
```