# Windows Runtime support
Unity包括Windows运行时对IL2CPP的支持在通用Windows平台上和Xbox One平台。使用Windows运行时支持可直接从托管代码（脚本和DLL）调用本机系统Windows运行时API以及自定义.winmd文件。

要在IL2CPP中自动启用Windows运行时支持，请转到Player settings（**Edit > Project Settings**，然后选择**Player**类别），导航到**Configuration**部分，并将**Api Compatibility Level**设置为 **.NET 4.6**或 **.NET Standard 2.0**。  
![](IL2CPP-4.png)  
*The Configuration section of the Player settings. The options shown above change depending on your chosen build platform.*

启用了Windows运行时支持的Unity会自动引用Windows运行时API（例如Universal Windows Platform上的Windows.winmd）。要使用自定义.winmd文件，请将它们（以及所有附带的DLL）导入到Unity项目文件夹中。然后，使用插件检查器为目标平台配置文件。  
![](IL2CPP-5.png)  
*Use the Plugin Inspector to configure custom .winmd files for specific platforms*

在Unity项目的脚本中您可以使用ENABLE_WINMD_SUPPORT #define指令来检查您的项目是否已启用Windows运行时支持。在调用.winmd Windows API或自定义.winmd脚本之前使用此功能，以确保它们可以运行，并确保所有与Windows不相关的脚本都将其忽略。请注意，这仅在C＃脚本中受支持。请参阅下面的示例。
```cs
void Start() {
  #if ENABLE_WINMD_SUPPORT
    Debug.Log("Windows Runtime Support enabled");
    // Put calls to your custom .winmd API here
  #endif
}
```