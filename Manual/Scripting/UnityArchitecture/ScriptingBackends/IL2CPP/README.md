# IL2CPP
IL2CPP（C ++中间语言）是Unity开发的脚本后端在为各种平台构建项目时，可以用它替代Mono。使用**IL2CPP**建立项目时，Unity会为在所选平台创建本机二进制文件（例如，.exe，apk，.xap）之前，将其脚本的IL代码转换成C++。 IL2CPP的一些用途包括提高Unity项目的性能，安全性和平台兼容性。

有关使用IL2CPP的更多信息，请参考[Unity IL2CPP博客系列](https://blogs.unity3d.com/2015/05/06/an-introduction-to-ilcpp-internals/?_ga=2.126088654.2135631044.1601045365-1096585399.1561227707)和以下Unity手册页面。
* [Building a project using IL2CPP](https://docs.unity3d.com/Manual/IL2CPP-BuildingProject.html)
* [How IL2CPP works](https://docs.unity3d.com/Manual/IL2CPP-HowItWorks.html)
* [Optimising IL2CPP build times](https://docs.unity3d.com/Manual/IL2CPP-OptimizingBuildTimes.html)

有关哪些平台支持IL2CPP的详细信息，请参见[脚本限制](https://docs.unity3d.com/Manual/ScriptingRestrictions.html)页面。

IL2CPP以与Mono脚本编写后端相同的方式支持托管代码的调试。