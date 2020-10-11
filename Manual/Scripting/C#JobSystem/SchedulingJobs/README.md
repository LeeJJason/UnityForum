# Scheduling jobs
要在主线程中安排作业，您必须：
* Instantiate the job.
* Populate the job’s data.
* Call the [Schedule](https://docs.unity3d.com/ScriptReference/Unity.Jobs.IJobExtensions.Schedule.html) method.

调用计划会将作业放入作业队列中，以在适当的时间执行。计划后，您将无法中断工作。

注意：您只能从主线程调用Schedule。

## An example of scheduling a job
```cs
// Create a native array of a single float to store the result. This example waits for the job to complete for illustration purposes
NativeArray<float> result = new NativeArray<float>(1, Allocator.TempJob);

// Set up the job data
MyJob jobData = new MyJob();
jobData.a = 10;
jobData.b = 10;
jobData.result = result;

// Schedule the job
JobHandle handle = jobData.Schedule();

// Wait for the job to complete
handle.Complete();

// All copies of the NativeArray point to the same memory, you can access the result in "your" copy of the NativeArray
float aPlusB = result[0];

// Free the memory allocated by the result array
result.Dispose();
```