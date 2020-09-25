(Unity Version 2019.4)
## Referencing additional class library assemblies 
如果Unity项目需要访问默认情况下未编译的.NET类库API的一部分，则该项目可以通知Unity中的C＃编译器。行为取决于项目使用哪个.NET版本。

### .NET standard 2.0 profile
如果您的项目使用 .NET Standard 2.0 API兼容级别，则无需采取任何其他步骤即可使用.NET类库API的一部分。如果API的一部分似乎丢失了，则它可能不包含在 .NET Standard 2.0中。该项目可能需要改为使用 .NET 4.x API兼容级别。

### .NET 4.x profile
默认情况下，当您使用 .NET 4.x API兼容级别时，Unity引用以下程序集：
* mscorlib.dll
* System.dll
* System.Core.dll
* System.Runtime.Serialization.dll
* System.Xml.dll
* System.Xml.Linq.dll

使用csc.rsp文件来引用任何其他类库程序集。您可以将此文件添加到Unity项目的Assets目录中，并使用该文件将其他命令行参数传递给C＃编译器。例如，如果您的项目使用在System.Net.Http.dll程序集中定义的HttpClient类，则C＃编译器可能会产生以下初始错误消息：  
```
The type `HttpClient` is defined in an assembly that is not referenced. You must add a reference to assembly 'System.Net.Http, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.
```

要解决此错误，请将以下csc.rsp文件添加到项目中：
```
-r:System.Net.Http.dll
```
您应该按照上面的示例中所述引用类库程序集。您必须将程序集文件放置在Project的**Assets**文件夹或其子文件夹中。

### Switching between profiles
在使用csc.rsp文件引用类库程序集时，应格外小心。如果将**API Compatibility Level**从 .NET 4.x更改为 .NET Standard 2.0，并且Project中存在一个类似于上面示例中的csc.rsp，则C＃编译将失败。 .NET Standard 2.0版本中不存在System.Net.Http.dll程序集，因此C＃编译器无法找到它。  
csc.rsp文件可以包含特定于当前.NET概要文件的部分。如果对配置文件进行更改，则需要修改csc.rsp文件。