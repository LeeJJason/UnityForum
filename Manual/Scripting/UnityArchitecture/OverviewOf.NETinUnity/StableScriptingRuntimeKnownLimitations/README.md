## Stable scripting runtime: known limitations
Unity支持现代的.NET运行时。使用.NET运行时时，您可能会遇到以下问题：

### Code size
与传统脚本运行时相比，稳定的脚本运行时附带了更大的.NET类库API。这意味着代码大小通常更大。这种大小的增加可能非常重要，尤其是在大小受限和 Ahead-of-Time（AOT）平台上。  
为了减轻代码大小的增加：
1. 选择最小的.NET配置文件（请参阅.NET配置文件支持）。 
.NET Standard 2.0配置文件大约是 .NET 4.x配置文件大小的一半，因此请尽可能使用 .NET Standard 2.0配置文件。
2. 在Unity编辑器播放器设置中启用**Strip Engine Code**（转到**Edit > Project Settings**，然后选**Player**类别）。此选项静态分析项目中的托管代码，并删除所有未使用的代码。  
**Note**:该选项仅适用于**IL2CPP scripting backend**。