
## 1. 基础教程
包含了Unity Shader相关的基础知识，[学习教程](Tutorials/README.md)


## 代码
```shader
Shader "Arc/ArcHandWritePbr"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        _Tint("Tint", Color) = (1 ,1 ,1 ,1)
        [Gamma] _Metallic("Metallic", Range(0, 1)) = 0 //金属度要经过伽马校正
        _Smoothness("Smoothness", Range(0, 1)) = 0.5
        _LUT("LUT", 2D) = "white" {}
    }

    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            Tags 
            {
                "LightMode" = "ForwardBase"
            }
            CGPROGRAM
            #pragma target 3.0

            #pragma vertex vert
            #pragma fragment frag

            #include "UnityStandardBRDF.cginc" 

            struct appdata
            {
                float4 vertex : POSITION;
				float3 normal : NORMAL;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float2 uv : TEXCOORD0;
                float3 normal : TEXCOORD1;
                float3 worldPos : TEXCOORD2;
            };

            float4 _Tint;
            float _Metallic;
            float _Smoothness;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            sampler2D _LUT;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.worldPos = mul(unity_ObjectToWorld, v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                o.normal = UnityObjectToWorldNormal(v.normal);
                o.normal = normalize(o.normal);
                return o;
            }

            float3 fresnelSchlickRoughness(float cosTheta, float3 F0, float roughness)
            {
                return F0 + (max(float3(1.0 - roughness, 1.0 - roughness, 1.0 - roughness), F0) - F0) * pow(1.0 - cosTheta, 5.0);
            }

            fixed4 frag (v2f i) : SV_Target
            {
                i.normal = normalize(i.normal);
                float3 lightDir = normalize(_WorldSpaceLightPos0.xyz);
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
                float3 lightColor = _LightColor0.rgb;
                float3 halfVector = normalize(lightDir + viewDir);  //半角向量

                float perceptualRoughness = 1 - _Smoothness;

                float roughness = perceptualRoughness * perceptualRoughness;
                float squareRoughness = roughness * roughness;

                float nl = max(saturate(dot(i.normal, lightDir)), 0.000001);//防止除0
                float nv = max(saturate(dot(i.normal, viewDir)), 0.000001);
                float vh = max(saturate(dot(viewDir, halfVector)), 0.000001);
                float lh = max(saturate(dot(lightDir, halfVector)), 0.000001);
                float nh = max(saturate(dot(i.normal, halfVector)), 0.000001);			

                //漫反射部分
                float3 Albedo = _Tint * tex2D(_MainTex, i.uv);
                //float4 diffuseResult = float4(Albedo.rgb, 1);//理论上要除pi 但是unity为了保证效果和legacy效果差不多所以主光源没有除
                //UnitystandardBRDF.cginc 271行
                //环境光
                float3 ambient = 0.03 * Albedo;

                //镜面反射部分
                //D是镜面分布函数，从统计学上估算微平面的取向
                float lerpSquareRoughness = pow(lerp(0.002, 1, roughness), 2);//Unity把roughness lerp到了0.002
                float D = lerpSquareRoughness / (pow((pow(nh, 2) * (lerpSquareRoughness - 1) + 1), 2) * UNITY_PI);

                //几何遮蔽G 说白了就是高光
                float kInDirectLight = pow(squareRoughness + 1, 2) / 8;
                float kInIBL = pow(squareRoughness, 2) / 8;
                float GLeft = nl / lerp(nl, 1, kInDirectLight);
                float GRight = nv / lerp(nv, 1, kInDirectLight);
                float G = GLeft * GRight;

                //菲涅尔F

                //unity_ColorSpaceDielectricSpec.rgb这玩意大概是float3(0.04, 0.04, 0.04)，就是个经验值
                float3 F0 = lerp(unity_ColorSpaceDielectricSpec.rgb, Albedo, _Metallic);
                //float3 F = lerp(pow((1 - max(vh, 0)),5), 1, F0);//是hv不是nv
                float3 F = F0 + (1 - F0) * exp2((-5.55473 * vh - 6.98316) * vh);
                //镜面反射结果
                float3 SpecularResult = (D * G * F * 0.25)/(nv * nl);

                //漫反射系数
                float3 kd = (1 - F)*(1 - _Metallic);

                //直接光照部分结果
                float3 specColor = SpecularResult * lightColor * nl * FresnelTerm(1, lh) * UNITY_PI;
                float3 diffColor = kd * Albedo * lightColor * nl;
                float3 DirectLightResult = diffColor + specColor;


                //ibl部分
                half3 ambient_contrib = ShadeSH9(float4(i.normal, 1));
                /*
                half3 ambient_contrib = 0.0;
                ambient_contrib.r = dot(unity_SHAr, half4(i.normal, 1.0));
                ambient_contrib.g = dot(unity_SHAg, half4(i.normal, 1.0));
                ambient_contrib.b = dot(unity_SHAb, half4(i.normal, 1.0));
                */

                float3 iblDiffuse = max(half3(0, 0, 0), ambient + ambient_contrib);

                float mip_roughness = perceptualRoughness * (1.7 - 0.7 * perceptualRoughness);
                float3 reflectVec = reflect(-viewDir, i.normal);

                half mip = mip_roughness * UNITY_SPECCUBE_LOD_STEPS;
                half4 rgbm = UNITY_SAMPLE_TEXCUBE_LOD(unity_SpecCube0, reflectVec, mip); //根据粗糙度生成lod级别对贴图进行三线性采样

                float3 iblSpecular = DecodeHDR(rgbm, unity_SpecCube0_HDR);

                float2 envBDRF = tex2D(_LUT, float2(lerp(0, 0.99 ,nv), lerp(0, 0.99, roughness))).rg; // LUT采样

                float3 Flast = fresnelSchlickRoughness(max(nv, 0.0), F0, roughness);
                float kdLast = (1 - Flast) * (1 - _Metallic);

                float3 iblDiffuseResult = iblDiffuse * kdLast * Albedo;
                float3 iblSpecularResult = iblSpecular * (Flast * envBDRF.r + envBDRF.g);
                float3 IndirectResult = iblDiffuseResult + iblSpecularResult;

                /*
                float surfaceReduction = 1.0 / (roughness*roughness + 1.0); //Liner空间
                //float surfaceReduction = 1.0 - 0.28*roughness*perceptualRoughness;  //Gamma空间
                float oneMinusReflectivity = 1 - max(max(SpecularResult.r, SpecularResult.g), SpecularResult.b);
                float grazingTerm = saturate(_Smoothness + (1 - oneMinusReflectivity));
                float4 IndirectResult = float4(iblDiffuse * kdLast * Albedo + iblSpecular * surfaceReduction * FresnelLerp(F0, grazingTerm, nv), 1);		
                */

                float4 result = float4(DirectLightResult +IndirectResult, 1);

                return result;
            }

            ENDCG
        }
    }
}
```

# 参考文章
[如何在Unity中造一个PBR Shader轮子](https://zhuanlan.zhihu.com/p/68025039)
[Learn Opengl](https://learnopengl.com/)
[Learn Opengl CN](https://learnopengl-cn.github.io/)