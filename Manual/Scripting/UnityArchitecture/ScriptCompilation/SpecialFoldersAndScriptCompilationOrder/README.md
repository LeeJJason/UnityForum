# Special folders and script compilation order
Unity保留了一些项目文件夹名称，以指示内容具有特殊用途。其中一些文件夹会影响脚本编译的顺序。这些文件夹名称是：
* Assets
* Editor
* Editor default resources
* Gizmos
* Plugins
* Resources
* Standard Assets
* StreamingAssets

有关这些文件夹的更多信息，请参见[特殊文件夹名称](https://docs.unity3d.com/Manual/SpecialFolders.html)。

# Predefined assemblies
Unity编译脚本根据脚本文件在项目文件夹结构中的位置，分为四个不同的阶段。 Unity为每个阶段创建一个单独的CSharp项目文件（.csproj）和一个预定义的程序集。（如果没有符合编译阶段要求的脚本，Unity不会创建相应的项目文件或程序集。）

当脚本引用在不同阶段（因此位于不同程序集中）编译的类时，编译顺序很重要。基本规则是，不能引用在当前阶段之后的阶段中编译的任何内容。在当前阶段或更早的阶段中编译的所有内容都是完全可用的。

编译阶段如下：

|Phase|Assembly name|Script files
|:----|:------------|:--------
|1|Assembly-CSharp-firstpass|在Standard Assets, Pro Standard Assets 和 Plugins文件夹中的所有运行脚本
|2|Assembly-CSharp-Editor-firstpass|在Standard Assets, Pro Standard Assets 和 Plugins文件夹中所有名为Editor文件夹中的编辑器脚本
|3|Assembly-CSharp|不在名为Editor的文件夹中的所有其他脚本。
|4|Assembly-CSharp-Editor|其余所有脚本（位于名为Editor的文件夹中的脚本）。

注意：Standard Assets仅在Assets中的根目录起作用。

您可以通过创建程序集定义文件，使用自己的程序集来组织项目中的脚本。定义自己的程序集可以减少不相关的代码更改时需要重新编译的代码量，并可以提供对依赖项的更多控制到其他程序集。有关更多信息，请参见[脚本编译-程序集定义文件](https://docs.unity3d.com/Manual/ScriptCompilationAssemblyDefinitionFiles.html)。