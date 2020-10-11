# Running Editor Script Code on Launch
有时，在Unity启动后立即能够在项目中运行一些编辑器脚本代码而无需用户采取措施是很有用的。您可以通过将**InitializeOnLoad**属性应用于具有**静态构造函数**的类来实现。静态构造函数是与类同名的函数，声明为静态且没有返回类型或参数（请参见此处以获取更多信息）：
```cs
using UnityEngine;
using UnityEditor;

[InitializeOnLoad]
public class Startup {
    static Startup()
    {
        Debug.Log("Up and running");
    }
}
```

始终保证在使用任何静态函数或类的实例之前将调用静态构造函数，但是InitializeOnLoad属性可确保在编辑器启动时调用它。

如何使用此技术的一个示例是在编辑器中设置常规回调（实际上是“frame update”）。 EditorApplication类具有一个名为update的委托，该委托在运行编辑器时每秒被调用多次。要在项目启动时启用此委托，可以使用如下代码：
```cs
using UnityEditor;
using UnityEngine;

[InitializeOnLoad]
class MyClass
{
    static MyClass ()
    {
        EditorApplication.update += Update;
    }

    static void Update ()
    {
        Debug.Log("Updating");
    }
}

```