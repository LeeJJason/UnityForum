## Shader Reference
在Unity中内置的渲染管线中写shader的方式如下：
* 作为 surface shaders,
* 作为 vertex and fragment shaders, or
* 作为 fixed function shaders.

决定哪个shader更适合你的需要，参考[shader tutorial](https://docs.unity3d.com/Manual/Shaders.html).
不管哪种类型，shader代码始终由 ShaderLab 语言封装，该语言组织shader的结构。这有一个创建没有shader代码的 ShaderLab 封装例子：
```
Shader "MyShader" {
    Properties {
        _MyTexture ("My Texture", 2D) = "white" { }
        // place other properties here, such as colors or vectors.
    }
    SubShader {
        // place the shader code here for your:
        // - surface shader,
        // - vertex and program shader, or
        // - fixed function shader
    }
    SubShader {
        // a simpler version of the subshader above goes here.
        // this version is for supporting older graphics cards.
    }
}
```

学习更多的shader基础和固定函数shader，参考[ShaderLab syntax](ShaderLabSyntax/README.md).其他支持的shader类型，参考[Writing Surface Shaders](WritingSurfaceShaders/README.md)或者[Writing vertex and fragment shaders](WritingVertexAndFragmentShaders/README.md)。你也能使用shader配合[post-processing effects](https://docs.unity3d.com/Manual/PostProcessingOverview.html)创建全拼过滤器和其他有趣的效果。


## 请参见

* [Writing Surface Shaders](WritingSurfaceShaders/README.md).
* [Writing vertex and fragment shaders](WritingVertexAndFragmentShaders/README.md).
* [ShaderLab Syntax Reference](ShaderLabSyntax/README.md).
* [Shader Assets](ShaderAssets/README.md).
* [Advanced ShaderLab Topics](AdvancedShaderLabTopics/README.md).