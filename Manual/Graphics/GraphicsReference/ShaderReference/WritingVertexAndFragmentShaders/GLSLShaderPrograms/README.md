## GLSL Shader programs
除了使用[Cg / HSL着色器程序](../README.md)之外，OpenGL着色语言（GLSL）着色器
可以中直接编写。
但是，只建议在测试时使用原始GLSL，或者当您知道您只针对Mac OS X、OpenGL ES移动设备或Linux时使用。在所有的正常情况下，当需要的时候Unity会交叉编译Cg/HLSL到优化的GLSL。

## GLSL snippets
GLSL程序片段是在`GLSLPROGRAM`和`ENDGLSL`关键字之间编写的。
在GLSL中，所有的着色器函数入口点都必须调用`main()`。当Unity加载GLSL着色器时，它为顶点程序加载源程序一次，使用`VERTEX`预定义，再为片段程序加载源程序一次，使用`FRAGMENT`预定义。因此，在GLSL代码片段中分离顶点和片段程序部分的方法是用`#ifdef VERTEX .. #endif`和`#ifdef FRAGMENT .. #endif`。每个GLSL片段必须同时包含一个顶点程序和一个片段程序。

标准包含文件与为Cg / HLSL着色器提供的文件匹配；他们只是有一个.glslinc扩展名：
```
UnityCG.glslinc
```

顶点着色器的输入来自预定义的GLSL变量(gl_Vertex, gl_MultiTexCoord0，…)或用户定义的属性。通常只有切向量需要用户定义的属性:
```
attribute vec4 Tangent;
```

从顶点到片段程序的数据通过各种变量传递，例如：
```
varying vec3 lightDir; // vertex shader computes this, fragment shader uses this
```

## External OES textures
在Shader编译期间，Unity进行了一些预处理。例如，`texture2D/texture2DProj`基于图形API（GlES3，GLCore）用`texture/textureProj`功能替换。某些扩展不支持新约定，尤其是[GL_OES_EGL_image_external](https://www.khronos.org/registry/OpenGL/extensions/OES/OES_EGL_image_external.txt)。

如果要在GLSL着色器中采样外部纹理，请使用`textureExternal/textureProjExternal`调用代替`texture2D/texture2DProj`。

例：
```
gl_FragData[0] = textureExternal(_MainTex, uv);
```