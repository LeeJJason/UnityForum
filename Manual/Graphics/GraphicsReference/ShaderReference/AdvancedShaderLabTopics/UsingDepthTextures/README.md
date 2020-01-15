## Using Depth Textures
可以创建[Render Textures](https://docs.unity3d.com/Manual/class-RenderTexture.html)其中每个像素
包含高精[度深](https://docs.unity3d.com/ScriptReference/RenderTextureFormat.Depth.html)度值。这是最常用的一些效果需要场景的深度是可用的(例如，软粒子，屏幕空间环境遮挡和半透明都需要场景的深度)。[Image Effects](https://docs.unity3d.com/Packages/com.unity.postprocessing@latest)也经常使用深度纹理。

深度纹理的像素值在0到1之间，呈非线性分布。精度通常是32位或16位，这取决于配置和使用的平台。从深度纹理读取数据时，会返回一个范围在0到1之间的高精度值。如果你需要与摄像机保持一定的距离或其他线性0-1值，使用辅助宏手动计算(参见下面)。
大多数现代硬件和图形api都支持深度纹理。特殊要求如下:
* Direct3D 11+ (Windows), OpenGL 3+ (Mac/Linux), OpenGL ES 3.0+ (Android/iOS), Metal (iOS) and consoles like PS4/Xbox One 都支持depth textures。
* OpenGL ES 2.0 (iOS/Android)需要[GL_OES_depth_texture](https://www.khronos.org/registry/OpenGL/extensions/OES/OES_depth_texture.txt)扩展被指定。
* WebGL 需要 [WEBGL_depth_texture](https://www.khronos.org/registry/webgl/extensions/WEBGL_depth_texture/) 扩展。

### Depth Texture Shader helper macros
大多数情况下，深度纹理用于渲染来自相机的深度。[UnityCG.cginc包含文件](../../WritingVertexAndFragmentShaders/Built-inShaderIncludeFiles/README.md)包含一些宏来处理上述复杂性在这种情况下:
* **UNITY_TRANSFER_DEPTH(o)**: 计算顶点的eye space深度并将其输出到o中(必须是float2)。渲染时在顶点程序中使用它
进入深度纹理。在具有本机深度纹理的平台上，这个宏什么都不做，因为Z缓冲值是隐式呈现的。
* **UNITY_OUTPUT_DEPTH(i)**: 从i(必须是float2)处获得eye space深度。当渲染到深度纹理时，在片段程序中使用它。在具有本机深度纹理的平台上，此宏总是返回零，因为Z缓冲区值是隐式呈现的。
* **COMPUTE_EYEDEPTH(i)**: 计算顶点的eye space深度，并在o中输出。当不渲染到深度纹理时，在顶点程序中使用它。
* **DECODE_EYEDEPTH(i)/LinearEyeDepth(i)**: 给定深度纹理i的高精度值，返回相应的眼空间深度。
* **Linear01Depth(i)**: 给定深度纹理i的高精度值，返回相应的0 - 1范围内的线性深度。

**注意**:在DX11/12、PS4、XboxOne和Metal上，Z缓冲范围为1-0，并且定义了UNITY_REVERSED_Z。在其他平台上，范围是0-1。

例如，这个着色器渲染游戏对象的深度：
```cs
Shader "Render Depth" {
    SubShader {
        Tags { "RenderType"="Opaque" }
        Pass {
            CGPROGRAM

            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            struct v2f {
                float4 pos : SV_POSITION;
                float2 depth : TEXCOORD0;
            };

            v2f vert (appdata_base v) {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                UNITY_TRANSFER_DEPTH(o.depth);
                return o;
            }

            half4 frag(v2f i) : SV_Target {
                UNITY_OUTPUT_DEPTH(i.depth);
            }
            ENDCG
        }
    }
}
```

## See also
* [Camera Depth Textures](../CameraDepthTexture/README.md)
* [Post Processing package](https://docs.unity3d.com/Packages/com.unity.postprocessing@latest)