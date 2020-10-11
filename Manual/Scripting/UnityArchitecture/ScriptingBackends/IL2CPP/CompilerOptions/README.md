# Compiler options
使用IL2CPP脚本后端时，可以控制il2cpp.exe如何生成C ++代码。具体来说，C＃属性可用于启用或禁用下面列出的以下运行时检查。

|Option|Description|Default
|:---  |:----------|:----
|Null checks|如果启用此选项，则IL2CPP生成的C ++代码将包含空检查，并在必要时引发托管NullReferenceException异常。如果禁用此选项，则将不将空检查发送到生成的C ++代码中。对于某些项目，禁用此选项可以提高运行时性能。但是，对生成的代码中的空值的任何访问都不会受到保护，并且可能导致错误的行为。通常，游戏将在取消引用null值后立即崩溃，但是我们不能保证。请谨慎禁用此选项。|Enabled
|Array bounds checks|如果启用此选项，则IL2CPP生成的C ++代码将包含数组范围检查，并在必要时引发托管的IndexOutOfRangeException异常。如果禁用此选项，则数组边界检查将不会发出到生成的C ++代码中。对于某些项目，禁用此选项可以提高运行时性能。但是，对在生成的代码中具有无效索引的数组的任何访问都将不受保护，并且可能导致错误的行为，包括从任意内存位置读取或写入任意内存位置。在大多数情况下，这些内存访问将在没有任何直接副作用的情况下发生，并且可能会悄悄破坏游戏的状态。禁用此选项时要格外小心。|Enabled
|Divide by zero checks|如果启用此选项，则IL2CPP生成的C ++代码将包含零除以检查整数除法，并在必要时引发托管的DivideByZeroException异常。如果禁用此选项，则整数除法的零除检查将不会发送到生成的C ++代码中。对于大多数项目，应禁用此选项。仅在需要除以零的检查时才启用它，因为这些检查会花费运行时间。|Disable

可以使用**Il2CppSetOptions**属性在C＃代码中启用或禁用运行时检查。要使用此属性，请在计算机上Unity编辑器安装的IL2CPP目录中找到*Il2CppSetOptionsAttribute.cs*源文件。 （Data\il2cpp on Windows, Contents/Frameworks/il2cpp on OS X）。将此源文件复制到项目中的Assets文件夹中。

现在，使用下面的示例中的属性。
```cs
[Il2CppSetOption(Option.NullChecks, false)]
public static string MethodWithNullChecksDisabled()
{
    var tmp = new object();
    return tmp.ToString();
}
```
您可以将Il2CppSetOptions属性应用于类型，方法和属性。 Unity使用来自最本地范围的属性。
```cs
[Il2CppSetOption(Option.NullChecks, false)]
public class TypeWithNullChecksDisabled
{
    public static string AnyMethod()
    {
        // Null checks will be disabled in this method.
        var tmp = new object();
        return tmp.ToString();
    }

    [Il2CppSetOption(Option.NullChecks, true)]
    public static string MethodWithNullChecksEnabled()
    {
        // Null checks will be enabled in this method.
        var tmp = new object();
        return tmp.ToString();
    }
}
```

```cs
public class SomeType
{
    [Il2CppSetOption(Option.NullChecks, false)]
    public string PropertyWithNullChecksDisabled
    {
        get
        {
            // Null checks will be disabled here.
            var tmp = new object();
            return tmp.ToString();
        }
        set
        {
            // Null checks will be disabled here.
            value.ToString();
        }
    }

    public string PropertyWithNullChecksDisabledOnGetterOnly
    {
        [Il2CppSetOption(Option.NullChecks, false)]
        get
        {
            // Null checks will be disabled here.
            var tmp = new object();
            return tmp.ToString();
        }
        set
        {
            // Null checks will be enabled here.
            value.ToString();
        }
    }
}
```