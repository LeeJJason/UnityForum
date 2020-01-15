## ShaderLab: SubShader
Unity中的每个**shader**由一系列**subshaders**组成。当Unity需要显示**mesh**，则会找到使用的**shader**，然后选择第一个能在玩家显卡上运行的subshader。

### Syntax
```
Subshader { [Tags] [CommonState] Passdef [Passdef ...] }
```
将子着色器定义为可选标记、公共状态和一组Pass定义。

### Details
一个subshader定义了一系列[rendering passes](ShaderLabPass/README.md)，并可以选择设置所有pass通用的任何状态。此外，subshader 特定的[Tags](ShaderLabPassSubShaderTags/README.md)可以设置。

当Unity选择了用于渲染的subshader，它将为每个定义的Pass渲染一次对象（由于灯光交互，可能会渲染多次）。由于对象的每次渲染都是一项昂贵的操作，因此您希望以尽可能少的通过次数定义着色器。当然，有时在某些图形硬件上，无法一次完成所需的效果。那么您别无选择，只能使用多次通过。

每个Pass定义可以是一个 [regular Pass](ShaderLabPass/README.md), [Use Pass](ShaderLabUsePass/README.md) 或者 [Grab Pass](ShaderLabGrabPass/README.md)。

任何允许Pass定义的语句也可以出现在子着色块中。这将使所有的Pass使用这个“共享”状态。

### Example
```
// ...
SubShader {
    Pass {
        Lighting Off
        SetTexture [_MainTex] {}
    }
}
// ...
```
这个subshader定义了一个Pass，关闭了所有灯管，只用_MainTex来显示一个网格。