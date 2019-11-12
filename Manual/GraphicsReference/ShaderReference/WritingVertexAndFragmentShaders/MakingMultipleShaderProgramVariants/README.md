## Making multiple shader program variants
通常保持着色器代码的大部分固定是很方便的，但也允许产生稍微不同的着色器“变体”。这通常被称为“mega shaders” 或 “uber shaders”，通过使用不同的预处理器指令多次编译着色器代码来实现。

要在Unity中实现此目的，您可以在[着色器代码段](../README.md)中添加#pragma multi_compile或#pragma shader_feature指令。这也适用于[表面着色器](../../WritingSurfaceShaders/README.md)。

在运行时，Unity从Material关键字（[Material.EnableKeyword](https://docs.unity3d.com/ScriptReference/Material.EnableKeyword.html)和[Material.DisableKeyword](https://docs.unity3d.com/ScriptReference/Material.DisableKeyword.html)）或全局着色器关键字（[Shader.EnableKeyword](https://docs.unity3d.com/ScriptReference/Shader.EnableKeyword.html)和[Shader.DisableKeyword](https://docs.unity3d.com/ScriptReference/Shader.DisableKeyword.html)）中选择适当的着色器变体。

## How multi_compile works
指令示例：
```
#pragma multi_compile FANCY_STUFF_OFF FANCY_STUFF_ON
```
此示例指令生成两个着色器变体：一个具有FANCY_STUFF_OFF定义的着色器，另一个具有FANCY_STUFF_ON。在运行时，Unity基于“材质”或全局着色器关键字激活其中之一。如果这两个关键字均未启用，则Unity使用第一个（在本示例中为FANCY_STUFF_OFF）。
您可以在multi_compile行上添加两个以上的关键字。例如：
```
#pragma multi_compile SIMPLE_SHADING BETTER_SHADING GOOD_SHADING BEST_SHADING
```
这个例子指令生产四种着色器变种：SIMPLE_SHADING，BETTER_SHADING，GOOD_SHADING，和BEST_SHADING。

要生成未定义预处理器宏的着色器变体，请添加一个下划线（__）名称。这是避免用完两个关键字的一种常用技术，因为在一个项目中可以使用的数量是有限制的（请参阅后面有关[关键字限制](#keyword-limits) limits)的部分）。例如：
```
#pragma multi_compile __ FOO_ON
```
该指令产生两个着色器变体：一个未定义（__），一个定义FOO_ON。

## Difference between shader_feature and multi_compile
`shader_feature`与`multi_compile`非常相似。唯一的区别是Unity `shader_feature`在最终版本中不包括未使用的着色器变体。因此，您应该使用`shader_feature`从材质设置的关键字，而`multi_compile`最好使用从全局代码设置的关键字。

此外，这是一个简写符号只是一个关键词：
```
#pragma shader_feature FANCY_STUFF
```
这只是#pragma shader_feature _ FANCY_STUFF的快捷方式。它扩展为两个着色器变体（第一个未定义；第二个定义）。

## Combining several multi_compile lines
如果提供多行multi_compile，Unity将为所有可能的行组合编译生成的着色器。例如：
```
#pragma multi_compile A B C
#pragma multi_compile D E
```
这将为第一行生成三个变体，为第二行生成两个变体。总共产生了六个着色器变体（A + D，B + D，C + D，A + E，B + E，C + E）。

将每行`multi_compile`都视为控制单个着色器“功能”。请记住，以这种方式着色器变体的总数快速增长。例如，十个`multi_compile` 功能（每个功能有两个选项）总共产生1024个着色器变体。

## Keyword limits
使用Shader变体时，Unity中限制为256个关键字，而Unity内部使用它们中的大约60个（因此降低了可用限制数量）。在Unity项目中全局启用了关键字，因此在多个不同的着色器中定义多个关键字时，请注意不要超过限制。

### Local keywords
**shader_feature**和**multi_compile**的主要缺点是，它们中定义的所有关键字都会对Unity的全局关键字计数限制（256个全局关键字加上64个局部关键字）有所贡献。为避免此问题，可以使用不同的着色器变体指令：**shader_feature_local**和**multi_compile_local**。
* **shader_feature_local**：类似于**shader_feature**，但枚举关键字是局部的。
* **multi_compile_local**：类似于**multi_compile**，但枚举关键字是局部的。

局部指令将定义的关键字保存在特定的那个着色器，而不是将它们应用于整个项目。因此，应该使用局部关键字而不是全局关键字，除非您计划通过全局API启用那些特定的关键字。

当您开始使用本地关键字时，您可能会看到性能上的变化，但这取决于您的项目是如何设置的。每个着色器的本地和全局关键字的总数影响性能:在理想的设置中，使用更多的本地关键字和更少的全局关键字，以减少每个着色器的关键字总数。

如果全局和本地关键字具有相同的名称，则Unity会优先考虑本地关键字。

### Limitations
* 您不能将本地关键字与进行全局关键字更改的API一起使用（例如Shader.EnableKeyword或CommandBuffer.EnableShaderKeyword）。
* 每个着色器最多有64个唯一的本地关键字。
* 如果材料启用了本地关键字，并且其着色器变为没有声明的着色器，则Unity将创建一个新的全局关键字。

### Example
```
#pragma multi_compile_local __ FOO_ON
```
这个指令产生了两个着色器变量:一个没有定义任何东西(__)，另一个将FOO_ON定义为本地关键字。
启用本地关键字的过程与启用全局关键字的过程相同：
```
public Material mat;
Private void Start()
{
    mat.EnableKeyword("FOO_ON");
}
```

## Built-in multi_compile shortcuts
对于编译多个着色器变体，有几种“快捷”表示法。这些主要是处理在Unity中不同的光源，阴影和光照贴图类型。有关详细信息，请参阅[rendering pipeline](https://docs.unity3d.com/Manual/SL-RenderPipeline.html)的文档。
* `multi_compile_fwdbase`编译[PassType.ForwardBase](https://docs.unity3d.com/ScriptReference/Rendering.PassType.ForwardBase.html)所需的所有变体。这些变体处理不同的光照贴图类型，并且启用或禁用了主方向光的阴影。
* `multi_compile_fwdadd`编译[PassType.ForwardAdd](https://docs.unity3d.com/ScriptReference/Rendering.PassType.ForwardAdd.html)所需的变体。这些编译变体处理定向，聚光或点光源类型，以及带有cookie纹理的变体。
* `multi_compile_fwdadd_fullshadows`与multi_compile_fwdadd相同，但还包括灯光具有实时阴影的功能。
* `multi_compile_fog`扩展为多种变体以处理不同的雾类型 (off/linear/exp/exp2)。

大多数内置的快捷方式会产生许多着色器的变体。如果您知道项目不需要它们，您可以使用#pragma skip_variant来跳过编译它们。例如:
```
#pragma multi_compile_fwdadd
#pragma skip_variants POINT POINT_COOKIE
```
该指令会跳过所有包含POINT或POINT_COOKIE的变体。

## Shader hardware variants
使用着色器硬变体可以为不同级别的硬件功能提供一组经过特殊优化的变体。您可以创建简化的着色器，以在单个目标平台（例如OpenGL ES）中的高端和低端硬件上高效运行。

要生成着色器硬件变体，可以添加`#pragma hardware_tier_variant renderer`，其中`renderer`是[shader program pragmas](../README.md)可用的渲染平台之一。使用这个#pragma, Unity会为每个着色器生成三个不同的着色器，不管其他的关键字是什么。每个变体都有以下定义之一:
```
UNITY_HARDWARE_TIER1
UNITY_HARDWARE_TIER2
UNITY_HARDWARE_TIER3
```
使用它们为较低或较高端的硬件编写条件回退或额外的特性。在Unity编辑器中，您可以在图形仿真菜单中测试任何层，它允许您在每个层之间进行更改。
为了帮助保持这些变量的影响尽可能小，Unity在玩家中只加载一组着色器。相同的着色器(例如，如果您只为TIER1编写一个专门的版本，而其他所有的版本都是相同的)不会占用磁盘上的任何额外空间。
在加载时，Unity检查它正在使用的GPU，并自动检测一个层的值。如果它不能自动检测的GPU，它默认为最高层。你可以设置`Shader.globalShaderHardwareTier`覆盖这个层值，但你必须在Unity加载任何你想改变的着色器之前这样做。在加载主场景之前，最好是在预加载场景中进行设置。

这些着色器硬件层与播放器的质量设置无关。它们是通过玩家运行的GPU的相对能力来检测的。

## Platform shader settings
除了为不同的硬件层调整你的着色器代码，你可能想要调整Unity的内部 #defines (例如，你可能想要在手机上强制级联阴影地图)。有关如何做到这一点的详细信息，请参阅[UnityEditor.Rendering.PlatformShaderSettings](https://docs.unity3d.com/ScriptReference/Rendering.PlatformShaderSettings.html)，它提供了当前支持的覆盖每层的特性的列表。
使用[UnityEditor.Rendering.EditorGraphicsSettings.SetShaderSettingsForPlatform](https://docs.unity3d.com/ScriptReference/Rendering.EditorGraphicsSettings.SetShaderSettingsForPlatform.html)调整每个平台每个平台的平台着色器设置。
如果`PlatformShaderSettings`设置为不同的层不相同，则即使`#pragma hardware_tier_variants`缺少，Unity也会为着色器生成层变体。