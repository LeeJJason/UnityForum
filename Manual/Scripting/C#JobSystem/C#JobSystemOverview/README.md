# C# Job System Overview

## How the C# Job System works
Unity C＃ Job System允许用户编写与Unity的其余部分良好交互的多线程代码，并使编写正确代码的过程变得更加容易。

编写多线程代码可以提供高性能的好处。这些包括显着提高帧速率。将Burst编译器与C＃作业一起使用可以提高代码生成质量，这也可以大大减少移动设备上的电池消耗。

C＃ Job System的一个重要方面是，它与Unity内部使用的功能（Unity的本机工作系统）集成在一起。用户编写的代码和Unity共享工作线程。这种合作避免了创建比CPU内核更多的线程，这将导致CPU资源争用。

有关更多信息，请观看[Unity at GDC - Job System & Entity Component System](https://www.youtube.com/watch?v=kwnb9Clh2Is&t=1s)。