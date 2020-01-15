## Built-in shader include files
Unity包含多个文件，[着色程序](../README.md)可以使用这些文件来引入预定义的变量和帮助函数。这是通过标准#include指令完成的，例如：
```
CGPROGRAM
// ...
#include "UnityCG.cginc"
// ...
ENDCG
```

Unity中的Shader包含文件带有.cginc扩展名，内置文件包括：
* `HLSLSupport.cginc` - （自动包含）用于跨平台着色器编译的辅助宏和定义。
* `UnityShaderVariables.cginc` - （自动包含）常用的全局变量。
* `UnityCG.cginc` - 常用的[辅助函数](../Built-inShaderHelperFunctions/README.md)。
* `AutoLight.cginc` - 照明和阴影功能，例如[表面着色器](../../WritingSurfaceShaders/README.md)内部使用此文件。
* `Lighting.cginc` - 标准表[表面着色器](../../WritingSurfaceShaders/README.md)照明模型；在编写表面着色器时自动包含在内。
* `TerrainEngine.cginc` - 用于辅助功能地形和植被着色器。

如果想查看辅助代码中到底做了什么，可以在Unity应用程序（在Windows中为 **{unity install path} /Data/CGIncludes/UnityCG.cginc**，在Mac为 **/Applications/Unity/Unity.app/Contents/CGIncludes/UnityCG.cginc**）中找到这些文件。

## HLSLSupport.cginc
编译**CGPROGRAM**着色器时会自动包含此文件（但HLSLPROGRAM着色器不包含此文件）。它声明了各种[预处理器宏](../PredefinedShaderPreprocessorMacros/README.md)，以帮助开发多平台着色器。

## UnityShaderVariables.cginc
编译CGPROGRAM着色器时会自动包含此文件（但HLSLPROGRAM着色器不包含此文件）。它声明了着色器中常用的各种[内置全局变量](../Built-inShaderVariables/README.md)。

## UnityCG.cginc
该文件通常包含在Unity着色器中。它声明了许多内置的[辅助函数](../Built-inShaderHelperFunctions/README.md)和数据结构。

### Data structures in UnityCG.cginc
* struct appdata_base：顶点着色器渲染输入中的位置，法线和一个纹理坐标。
* struct appdata_tan：具有位置，法线，切线和一个纹理坐标的顶点着色器输入。
* struct appdata_full：具有位置，法线，切线，顶点颜色和两个纹理坐标的顶点着色器输入。
* struct appdata_img：具有位置和一个纹理坐标的顶点着色器输入。