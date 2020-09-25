## Understanding Automatic Memory Management
Unity支持许多.NET版本。每个版本都为C＃代码提供了与.NET类库交互的不同API 接口。您可以在**Player settings**（转到 **Edit** > **Project Settings**，然后选择 **Player** 类别)中使用**Other Settings**面板中的**Api Compatibility Level**更改NET版本。

### Legacy scripting runtime
旧版脚本运行时支持两种不同的版本：.NET 2.0子集和.NET 2.0。两者都与Microsoft的.NET 2.0版本紧密结合。 .NET 2.0子集版本比.NET 4.x版本小，它允许访问大多数Unity项目使用的类库API。
对于大小受限制的平台（例如移动设备），它是理想的选择，并且它提供了一组可移植的API，以支持多平台。
默认情况下，大多数Unity项目应使用.NET Standard 2.0版本。

### Stable scripting runtime
稳定的脚本运行时支持两种不同的版本：.NET Standard 2.0和.NET4.x。 .NET Standard 2.0版本的名称可能会引起误解，因为它与旧版脚本运行时中的.NET 2.0和.NET 2.0子集版本无关。相反，Unity对.NET Standard 2.0版本的支持与.NET Foundation发布的同名版本匹配。 Unity中的.NET 4.x版本与.NET Framework中的.NET 4系列（.NET 4.5，.NET 4.6，.NET 4.7等）版本匹配。
仅将.NET 4.x配置文件用于与外部库兼容，或者在需要.NET Standard 2.0中不提供的功能时使用。

### Cross-platform compatibility
Unity旨在在所有平台上支持.NET Standard 2.0概要文件中的绝大多数API。尽管并非所有平台都完全支持.NET标准，但旨在实现跨平台兼容性的库应针对.NET Standard 2.0版本。 .NET 4.x版本包括一个更大的API范围，其中包括可能在很少或没有平台上工作的部分。

### Managed plugins
在Unity外部编译的托管代码插件可以与Unity中的.NET Standard 2.0配置文件或.NET 4.x配置文件一起使用。
下表描述了Unity支持的配置：  

|Managed plugin compiled against:|API Compatibility Level:<br/>.NET Standard 2.0|.NET 4.x|
|----|------|-----|
|.NET Standard|Supported|Supported
|.NET Framework|Limited|Supported
|.NET Core|Not Supported|Not Supported

**Note:**
* 针对任何版本的 .NET Standard编译的托管插件均可与Unity一起使用。
* 有限的支持表示如果 .NET Standard 2.0版本中存在 .NET Framework使用的所有API，则Unity支持配置。
但是 .NET Framework API是 .NET Standard 2.0配置文件的超集，因此某些API不可用。

### Transport Layer Security (TLS) 1.2
从2018.2开始，Unity在除WebGL之外的所有平台上提供完整的TLS 1.2支持。它通过UnityWebRequest API和所有 .NET 4.x API来执行此操作。  
如果可用，则通过平台特定的证书存储自动完成证书验证。在无法访问证书存储的地方，Unity使用嵌入式根证书存储。  
.NET 3.5及以下版本的TLS支持因平台而异，并且不能保证支持哪些功能。