Unity官方手册上讲Shader程序嵌入的小片段是用Cg/HLSL编写的，从“CGPROGRAM”开始，到“CGEND”结束。所以，Unity官方主要是用Cg/HLSL编写Shader程序片段。Unity官方手册也说明对于Cg/HLSL程序进行扩展也可以使用GLSL，不过Unity官方建议使用原生的GLSL进行编写和测试。如果不使用原生GLSL，你就需要知道你的平台必须是Mac OS X、OpenGL ES 2.0以上的移动设备或者是Linux。在一般情况下Unity会把Cg/HLSL交叉编译成优化过的GLSL。因此我们有多种选择，我们既可以考虑使用Cg/HLSL，也可以使用GLSL。不过由于Cg/HLSL更好的跨平台性，更倾向于使用Cg/HLSL编写Shader程序。

## Function
CG/HLSL 内置的[Function](Function/README.md),包含函数格式、返回值、target限制。


## 参考文章
[三大主流编程语言 HLSL/GLSL/Cg](https://www.cnblogs.com/maple-share/p/5415811.html)