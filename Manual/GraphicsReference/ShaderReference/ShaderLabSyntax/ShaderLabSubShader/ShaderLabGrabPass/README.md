## ShaderLab: GrabPass
GrabPass 是一个特殊的pass类型 - 它抓取屏幕三内中，并被绘制到一个图片中。改图片能用于后续pass做一些高级的继续图片的效果。

### Syntax
GrabPass 属于[subshade](../README.md)。它有两种形式：
* 仅仅 `GrabPass { } `抓取当前屏幕的内容到一个图片中。该图片可以再后面的pass中用 `_GrabTexture ` 变量访问。注意，这种形式的抓取将对使用它的每个对象执行耗时的屏幕抓取操作。
* `GrabPass { "TextureName" }`抓取当前屏幕的内容到一个图片中，但是只会在每帧在第一个对象使用改图片名字时执行一次。该图片可以再后面的pass中通过给点的图片名字访问。这是一个更有效的方法，当你有多个对象在场景中使用GrabPass

此外，GrabPass 能使用 [Name](../ShaderLabPass/ShaderLabName/README.md) 和 [Tags](../ShaderLabPass/ShaderLabPassTags/README.md) 命令。

## Example

下面是一种低效的方法来颠倒之前渲染的颜色:
```cs
Shader "GrabPassInvert"
{
    SubShader
    {
        // Draw ourselves after all opaque geometry
        Tags { "Queue" = "Transparent" }

        // Grab the screen behind the object into _BackgroundTexture
        GrabPass
        {
            "_BackgroundTexture"
        }

        // Render the object with the texture generated above, and invert the colors
        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "UnityCG.cginc"

            struct v2f
            {
                float4 grabPos : TEXCOORD0;
                float4 pos : SV_POSITION;
            };

            v2f vert(appdata_base v) {
                v2f o;
                // use UnityObjectToClipPos from UnityCG.cginc to calculate 
                // the clip-space of the vertex
                o.pos = UnityObjectToClipPos(v.vertex);
                // use ComputeGrabScreenPos function from UnityCG.cginc
                // to get the correct texture coordinate
                o.grabPos = ComputeGrabScreenPos(o.pos);
                return o;
            }

            sampler2D _BackgroundTexture;

            half4 frag(v2f i) : SV_Target
            {
                half4 bgcolor = tex2Dproj(_BackgroundTexture, i.grabPos);
                return 1 - bgcolor;
            }
            ENDCG
        }

    }
}
```
此shader有两个pass：第一个pass抓取在改对象渲染时在背后的对象，然后再第二个pass中使用。注意，相同的效果可以通过使用反转[blend mode](../ShaderLabPass/ShaderLabBlending/README.md)获得更好的性能。

## See Also
* [Regular Pass command](../ShaderLabPass/README.md).
* [Platform differences](../../../AdvancedShaderLabTopics/PlatformSpecificRenderingDifferences/README.md).
* [Built-in shader helper functions](../../../WritingVertexAndFragmentShaders/Built-inShaderHelperFunctions/README.md).