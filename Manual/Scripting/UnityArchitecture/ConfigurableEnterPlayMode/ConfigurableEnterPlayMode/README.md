# Configurable Enter Play Mode

播放模式是Unity的核心功能之一。它允许您通过工具栏上的“播放”按钮直接在编辑器中运行项目。当您进入“播放模式”时，您的项目将像在构建中一样开始并运行。当您退出播放模式时，在播放模式期间在编辑器中所做的任何更改都会重置。

当您在编辑器中进入播放模式时，Unity将执行两项重要操作，以确保您的项目在编辑器中的启动方式与在构建中的启动方式相同：
* 它重置脚本状态（也称为“域重新加载”）
* 重新加载场景

这两个操作需要一些时间来执行，并且时间随着脚本和场景变得更加复而增加。

在开发游戏或应用程序时，快速进入和退出“游戏模式”的能力是一个重要因素。进入和退出“播放模式”的速度越快，进行和测试更改的速度就越快。

由于开发过程中快速的迭代速度很重要，并且重置场景和脚本状态的时间可能会成为障碍，因此Unity可以配置进入播放模式时发生的情况，从而可以选择禁用其中一个或“域重新加载”和“场景重新加载”操作。这两个选项由可配置的进入播放模式功能提供。

下图显示了禁用“重新加载域”和“重新加载场景”设置的效果。
![](EnterPlayModeDiagram.svg)  
*The effects of disabling the Reload Domain and Reload Scene settings*

## How to configure Play Mode
要配置进入播放模式设置，请转至**File > Project Settings > Editor**，然后启用**Enter Play Mode Options**。启用此选项后，可以使用**reload domain**和**reload scene**的选项。  
![](EnterPlayModeSettings.png)  
*The Enter Play Mode settings in the Editor Project Settings window*

**Reload Domain**以启用 [Domain Reloading](https://docs.unity3d.com/Manual/DomainReloading.html)。启用**Reload Scene**以启用[Reload Scene](https://docs.unity3d.com/Manual/SceneReloading.html)。

**注意**：此功能是实验性的。并非所有软件包都经过验证是否与禁用的域和场景重载兼容，并且该功能的行为在Unity的未来版本中可能会更改。