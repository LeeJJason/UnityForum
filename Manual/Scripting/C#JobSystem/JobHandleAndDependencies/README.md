# JobHandle and dependencies
当您调用作业的[Schedule](https://docs.unity3d.com/ScriptReference/Unity.Jobs.IJobExtensions.Schedule.html)方法时，它将返回[JobHandle](https://docs.unity3d.com/ScriptReference/Unity.Jobs.JobHandle.html)。您可以在代码中使用JobHandle作为依赖项其他工作如果一个作业依赖于另一个作业的结果，则可以将第一个作业的JobHandle作为参数传递给第二个作业的Schedule方法，如下所示：
```cs
JobHandle firstJobHandle = firstJob.Schedule();
secondJob.Schedule(firstJobHandle);
```

## Combining dependencies
如果作业具有许多依赖关系，则可以使用[JobHandle.CombineDependencies](https://docs.unity3d.com/ScriptReference/Unity.Jobs.JobHandle.CombineDependencies.html)方法合并它们。 **CombineDependencies**允许您将它们传递给Schedule方法。
```cs
NativeArray<JobHandle> handles = new NativeArray<JobHandle>(numJobs, Allocator.TempJob);

// Populate `handles` with `JobHandles` from multiple scheduled jobs...

JobHandle jh = JobHandle.CombineDependencies(handles);
```

## Waiting for jobs in the main thread
使用JobHandle强制您的代码在主线程中等待您的作业完成执行。为此，请在JobHandle上调用[Complete](https://docs.unity3d.com/ScriptReference/Unity.Jobs.JobHandle.Complete.html)方法。至此，您知道主线程可以安全地访问作业正在使用的[NativeContainer](https://docs.unity3d.com/ScriptReference/Unity.Collections.LowLevel.Unsafe.NativeContainerAttribute.html)。

注意：在计划作业时，它们不会开始执行。如果您正在主线程中等待该作业，并且需要访问该作业正在使用的NativeContainer数据，则可以调用**JobJole.Complete**方法。此方法从内存缓存中清除作业，并开始执行过程。在**JobHandle**上调用**Complete**会将作业的**NativeContainer**类型的所有权返回给主线程。您需要在**JobHandle**上调用**Complete**，以再次安全地从主线程访问那些**NativeContainer**类型。也可以通过对来自作业依赖项的JobHandle调用Complete来将所有权返回给主线程。例如，您可以在jobA上调用Complete，也可以在jobB上调用Complete，这依赖于jobA。两者都导致jobA使用的NativeContainer类型在调用Complete之后可以安全地访问主线程。

否则，如果您不需要访问数据，则需要显式刷新批处理。为此，请调用静态方法[JobHandle.ScheduleBatchedJobs](https://docs.unity3d.com/ScriptReference/Unity.Jobs.JobHandle.ScheduleBatchedJobs.html)。请注意，调用此方法可能会对性能产生负面影响。

## An example of multiple jobs and dependencies
**Job code:**
```cs
// Job adding two floating point values together
public struct MyJob : IJob
{
    public float a;
    public float b;
    public NativeArray<float> result;

    public void Execute()
    {
        result[0] = a + b;
    }
}

// Job adding one to a value
public struct AddOneJob : IJob
{
    public NativeArray<float> result;
    
    public void Execute()
    {
        result[0] = result[0] + 1;
    }
}
```

**Main thread code:**
```cs
// Create a native array of a single float to store the result in. This example waits for the job to complete
NativeArray<float> result = new NativeArray<float>(1, Allocator.TempJob);

// Setup the data for job #1
MyJob jobData = new MyJob();
jobData.a = 10;
jobData.b = 10;
jobData.result = result;

// Schedule job #1
JobHandle firstHandle = jobData.Schedule();

// Setup the data for job #2
AddOneJob incJobData = new AddOneJob();
incJobData.result = result;

// Schedule job #2
JobHandle secondHandle = incJobData.Schedule(firstHandle);

// Wait for job #2 to complete
secondHandle.Complete();

// All copies of the NativeArray point to the same memory, you can access the result in "your" copy of the NativeArray
float aPlusB = result[0];

// Free the memory allocated by the result array
result.Dispose();
```