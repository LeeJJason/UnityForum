## ShaderLab: other commands

### Category
**Category**是它下面的任何命令的逻辑分组。这经常用于“继承”渲染状态。比如：你的shader可能有多个[subshaders]()，并且他们中的每个都需要关闭[fog](../ShaderLabSubShader/ShaderLabPass/README.md)，设置[blending](../ShaderLabSubShader/ShaderLabPass/ShaderLabBlending/README.md)融合，等。你可以使用Category做这个：
```cs
Shader "example" {
Category {
    Fog { Mode Off }
    Blend One One
    SubShader {
        // ...
    }
    SubShader {
        // ...
    }
    // ...
}
}
```

Category 块只影响着色器解析，它与“粘贴”类别内的任何状态到它下面的所有块完全相同。它根本不会影响着色器的执行速度。