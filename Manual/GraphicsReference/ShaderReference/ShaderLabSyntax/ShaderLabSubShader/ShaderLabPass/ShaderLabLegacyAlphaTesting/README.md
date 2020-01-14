## ShaderLab: Legacy Alpha Testing
 alpha test 是最后一个机会拒绝一个像素写入到屏幕上。
 
 **Note**：*当使用[fragment programs](../../../../WritingVertexAndFragmentShaders/README.md)时，AlphaTest命令无效;在大多数平台上，alpha测试是使用HLSL clip()函数在着色器中完成的。现在建议使用可编程着色器而不是SetTexture命令。*

 在计算出最终的输出颜色之后，可以选择将颜色的alpha值与固定值进行比较。如果测试失败，像素将不写入显示。

 ### Syntax
 ```cs
AlphaTest Off
 ```
 渲染所有的像素(默认)
 ```cs
AlphaTest comparison AlphaValue
 ```
 设置alpha test，只渲染alpha值在一定范围内的像素。

#### Comparison

|Comparison|DESC|
|:---------|:---|
|Greater|只渲染alpha值大于AlphaValue的像素。|
|GEqual|只渲染alpha值大于等于AlphaValue的像素。|
|Less|只渲染alpha值小于AlphaValue的像素。|
|LEqual|只渲染alpha值小于等于AlphaValue的像素。|
|Equal|只渲染alpha值等于AlphaValue的像素。|
|NotEqual|只渲染alpha值不等于AlphaValue的像素。|
|Always|渲染所有像素。 功能上与 AlphaTest Off 相等。|
|Never|不渲染所有像素。|

#### AlphaValue
介于0和1之间的浮点数。这也可以是对浮点数或范围属性的变量引用，在这种情况下应该使用标准方括号表示法([VariableName])来编写。

### Details
![](sl-alphatest2.jpg)
在这个图中，左边的树是使用**AlphaTest**呈现的。注意其中的像素是完全透明还是不透明的。中间的树只使用Alpha混合渲染——注意附近树枝的透明部分是如何覆盖远处的树叶的，因为有深度缓冲。右边的树是使用最后一个示例着色器呈现的——它实现了混合和alpha测试的组合以隐藏任何工件。

### Examples
最简单的例子，用alpha通道分配一个纹理。对象只有在alpha大于0.5时才可见
```cs
Shader "Simple Alpha Test" {
    Properties {
        _MainTex ("Base (RGB) Transparency (A)", 2D) = "" {}
    }
    SubShader {
        Pass {
            // Only render pixels with an alpha larger than 50%
            AlphaTest Greater 0.5
            SetTexture [_MainTex] { combine texture }
        }
    }
}
```
这本身并不好。让我们添加一些照明和使截止值可调:
```cs
Shader "Cutoff Alpha" {
    Properties {
        _MainTex ("Base (RGB) Transparency (A)", 2D) = "" {}
        _Cutoff ("Alpha cutoff", Range (0,1)) = 0.5
    }
    SubShader {
        Pass {
            // Use the Cutoff parameter defined above to determine
            // what to render.
            AlphaTest Greater [_Cutoff]
            Material {
                Diffuse (1,1,1,1)
                Ambient (1,1,1,1)
            }
            Lighting On
            SetTexture [_MainTex] { combine texture * primary }
        }
    }
}
```
在渲染植物和树木时，许多游戏都带有典型的alpha测试的硬边。一种方法是渲染对象两次。在第一轮中，我们只使用alpha测试来渲染超过50%不透明的像素。在第二步中，我们在被切掉的部分对图形进行字母混合，而不记录像素的深度。当远处的枝干覆盖了附近的枝干时，我们可能会有点困惑，但在实践中，这是很难看到的，因为叶子有很多视觉细节。
```cs
Shader "Vegetation" {
    Properties {
        _Color ("Main Color", Color) = (.5, .5, .5, .5)
        _MainTex ("Base (RGB) Alpha (A)", 2D) = "white" {}
        _Cutoff ("Base Alpha cutoff", Range (0,.9)) = .5
    }
    SubShader {
        // Set up basic lighting
        Material {
            Diffuse [_Color]
            Ambient [_Color]
        }
        Lighting On

        // Render both front and back facing polygons.
        Cull Off

        // first pass:
        // render any pixels that are more than [_Cutoff] opaque
        Pass {
            AlphaTest Greater [_Cutoff]
            SetTexture [_MainTex] {
                combine texture * primary, texture
            }
        }

        // Second pass:
        // render in the semitransparent details.
        Pass {
            // Dont write to the depth buffer
            ZWrite off
            // Don't write pixels we have already written.
            ZTest Less
            // Only render pixels less or equal to the value
            AlphaTest LEqual [_Cutoff]

            // Set up alpha blending
            Blend SrcAlpha OneMinusSrcAlpha

            SetTexture [_MainTex] {
                combine texture * primary, texture
            }
        }
    }
}
```
注意，我们在子着色器中有一些设置，而不是在单个的passes。子着色器中设置的任何状态都将作为默认值在其内部Pass中继承。