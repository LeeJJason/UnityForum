# Script serialization errors
序列化是将数据结构或对象状态转换为Unity以后可以存储和重构的格式的自动过程。 （有关更多信息，请参见[脚本序列化](https://docs.unity3d.com/Manual/script-Serialization.html)文档。）

在某些情况下，脚本序列化会导致错误。下面列出了其中一些修复程序。

## Calling Unity Scripting API from constructor or field initializers
在MonoBehaviour构造函数或字段初始化程序内部调用诸如GameObject.Find之类的脚本API会触发错误：“不允许从MonoBehaviour构造函数（或实例字段初始化程序）中调用查找，而应在Awake或Start中调用。”

通过在MonoBehaviour.Start中而不是在构造函数中调用Scripting API来解决此问题。

## Calling Unity Scripting API during deserialization
从标有System.Serializable的类的构造函数中调用诸如GameObject.Find之类的脚本API会触发错误：“不允许在序列化过程中调用查找，而应从Awake或Start进行调用。”

要解决此问题，请编辑您的代码，以使其在任何序列化对象的构造函数中都不会进行Scripting API调用。

## Thread-safe Unity Scripting API
大部分脚本API受上述限制的影响。 Unity脚本API的仅部分选择被豁免，可以在任何地方调用。这些是：
* Debug.Log
* Mathf functions
* Simple self-contained structs; for example math structs like Vector3 and Quaternion

为了降低序列化过程中出错的风险，仅调用自包含且不需要在Unity本身中获取或设置数据的API方法。如果没有其他选择，请仅调用这些。