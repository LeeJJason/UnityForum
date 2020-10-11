# Creating jobs
要在Unity中创建作业，您需要实现[IJob](https://docs.unity3d.com/ScriptReference/Unity.Jobs.IJob.html)接口。 **IJob**允许您计划与其他正在运行的作业并行运行的单个作业。

注意：“Job”是Unity中所有实现IJob接口的结构的统称。

要创建工作，您需要：
* 创建一个实现IJob的结构。
* 添加作业使用的成员变量（blittable 类型或NativeContainer类型）。
* 在结构中创建一个名为Execute的方法，并在其中执行作业。

执行作业时，**Execute**方法在单个内核上运行一次。
注意：在设计工作时，请记住，除了NativeContainer之外，它们都对数据副本进行操作。因此，从主线程中的作业访问数据的唯一方法是写入NativeContainer。

## An example of a simple job definition
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
```