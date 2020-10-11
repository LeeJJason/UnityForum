# What is a job system?
job system 通过创建[作业](https://en.wikipedia.org/wiki/Job_(computing))来管理[multithreaded code](https://docs.unity3d.com/Manual/JobSystemMultithreading.html)而不是线程。

job system管理跨多个核心的一组[工作线程](https://docs.microsoft.com/en-us/cpp/parallel/multithreading-creating-worker-threads)。每个[逻辑CPU内核](https://www.howtogeek.com/194756/cpu-basics-multiple-cpus-cores-and-hyper-threading-explained/)通常只有一个工作线程，以避免上下文切换（尽管它可能为操作系统或其他专用应用程序保留一些内核）。

job system将作业放入作业队列以执行。job system中的工作线程从作业队列中获取项目并执行它们。job system管理依赖关系并确保作业以适当的顺序执行。

## What is a job?
工作是完成一项特定任务的一小部分工作。作业接收参数并对数据进行操作，类似于方法调用的行为。作业可以是独立的，也可以依赖其他作业才能运行。

## What are job dependencies?
在复杂的系统（如游戏开发所需的系统）中，每个工作不太可能是独立的。一个工作通常是为下一个工作准备数据。 Jobs知道并支持依赖项才能完成这项工作。如果jobA对jobB有依赖关系，则作业系统将确保jobA在jobB完成之前不会开始执行。