## Custom Shader GUI
有时你有一个着色器一些有趣的数据类型不能很好地用内建的Unity材质编辑器来表示。Unity提供了一种覆盖默认的着色器属性的方法，这样你就可以定义你自己的着色器属性了。您可以使用此功能来定义自定义控件和数据范围验证。

为着色器的gui编写自定义编辑器的第一部分是定义一个需要[Custom Editor](../../ShaderLabSyntax/ShaderLabCustomEditor/README.md)的着色器。您为自定义编辑器使用的名称是将由Unity为材料编辑器查找的类。

要定义自定义编辑器，需要从ShaderGUI类扩展并将脚本放在assets目录下的一个编辑器文件夹下。

```cs
using UnityEditor;

public class CustomShaderGUI : ShaderGUI 
{
    public override void OnGUI (MaterialEditor materialEditor, MaterialProperty[] properties)
    {
        base.OnGUI (materialEditor, properties);
    }
}
```

任何具有自定义编辑器定义的着色器(**CustomEditor“CustomShaderGUI”**)将实例化上面列出的着色器gui类的实例并执行相关代码。

### A simple example
所以我们的情况是我们有一个着色器可以在两种模式下工作;它渲染标准的漫射照明，或者渲染50%的蓝色和绿色通道。
```cs
Shader "Custom/Redify" {
    Properties {
        _MainTex ("Base (RGB)", 2D) = "white" {}
    }
    SubShader {
        Tags { "RenderType"="Opaque" }
        LOD 200
        
        CGPROGRAM
        #pragma surface surf Lambert addshadow
        #pragma shader_feature REDIFY_ON

        sampler2D _MainTex;

        struct Input {
            float2 uv_MainTex;
        };

        void surf (Input IN, inout SurfaceOutput o) {
            half4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
            o.Alpha = c.a;

            #if REDIFY_ON
            o.Albedo.gb *= 0.5;
            #endif
        }
        ENDCG
    } 
    CustomEditor "CustomShaderGUI"
}
```

可以看到，着色器有一个关键字可用于设置:REDIFY_ON。这可以通过使用材质的shaderKeywords属性在每个材质的基础上进行设置。下面是一个ShaderGUI实例。
```cs
using UnityEngine;
using UnityEditor;
using System;

public class CustomShaderGUI : ShaderGUI
{
    public override void OnGUI(MaterialEditor materialEditor, MaterialProperty[] properties)
    {
        // render the default gui
        base.OnGUI(materialEditor, properties);

        Material targetMat = materialEditor.target as Material;

        // see if redify is set, and show a checkbox
        bool redify = Array.IndexOf(targetMat.shaderKeywords, "REDIFY_ON") != -1;
        EditorGUI.BeginChangeCheck();
        redify = EditorGUILayout.Toggle("Redify material", redify);
        if (EditorGUI.EndChangeCheck())
        {
            // enable or disable the keyword based on checkbox
            if (redify)
                targetMat.EnableKeyword("REDIFY_ON");
            else
                targetMat.DisableKeyword("REDIFY_ON");
        }
    }
}
```

有关更全面的ShaderGUI示例，请在‘Built-in shaders’ package参见StandardShaderGUI.cs文件及其 Standard.shader，可以从[Unity下载档案](http://unity3d.com/unity/download/archive)。

注意，上面的简单示例也可以通过使用materialpropertydrawer更简单地解决。添加以下行到自定义/重拨着色器的属性部分:
```cs
[Toggle(REDIFY_ON)] _Redify("Red?", Int) = 0
```

删除：
```cs
CustomEditor "CustomShaderGUI"
```

也可参考[MaterialPropertyDrawer](https://docs.unity3d.com/ScriptReference/MaterialPropertyDrawer.html)

ShaderGUI应该用于更复杂的shader gui解决方案，例如，材质属性相互依赖或者需要特殊的布局。您可以结合使用materialpropertydrawer和ShaderGUI类，参见StandardShaderGUI.cs。