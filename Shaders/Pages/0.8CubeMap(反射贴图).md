# Cube Map

## Slampe Cube Map
* Cubemap是一个由六个独立的正方形纹理组成的集合，它将多个纹理组合起来映射到一个单一纹理

* 包含6个2D纹理

* 通常被用来作为具有反射和（或）折射属性物体的反射源

* 根据该交点进行采样

* texCUBE(CubeMap, directionVec);
<br>![image](https://user-images.githubusercontent.com/74708198/224549231-c19a0f68-9484-4515-a97c-fdedd43de90b.png)

```HLSL
Shader "MyCustom/Chapter9_CubemapSampler"
{
    Properties
    {
        _CubeMap ("CubeMap",        CUBE) = "" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex       : POSITION;
                float3 normal       : NORMAL;
            };

            struct v2f
            {
                float4 vertexLocal  : TEXCOORD0;
                float4 vertex       : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.vertexLocal = v.vertex;
                return o;
            }

            samplerCUBE _CubeMap;

            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed4 col = texCUBE(_CubeMap, normalize(i.vertexLocal.xyz));
                return col;
            }
            ENDCG
        }
    }
}
```

## Reflect Cube Map
* 环境反射原理
  * 使用视线关于物体顶点法线的反射向量作为采样的方向向量
* 反射方向的计算
  * N为顶点的单位法向量
  * R为反射光的单位法向量
  * I是观察方向
  * R=2(N•I)N－I
* 使用公式计算
  * float3 R = reflect(L, N);

<br>![image](https://user-images.githubusercontent.com/74708198/224551306-12dca810-4847-44e8-88e0-ca2287f49ded.png)

```HLSL
Shader "MyCustom/Chapter9_CubemapReflection"
{
    Properties
    {
        _CubeMap ("CubeMap",        CUBE) = "" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex       : POSITION;
                float3 normal       : NORMAL;
            };

            struct v2f
            {
                float3 worldNormal  : TEXCOORD0;
                float3 worldPos     : TEXCOORD1;
                float4 vertex       : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);

                o.worldNormal = normalize(mul((float3x3)unity_ObjectToWorld, v.normal));
                o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                return o;
            }

            samplerCUBE _CubeMap;

            // 自定义的反射函数，i和n都是单位向量
            float3 myReflect(float3 i, float3 n)
            {
                return i - 2 * n * dot(i, n);
            }

            fixed4 frag (v2f i) : SV_Target
            {
                // 方向时顶点指向摄像机的位置
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos);

                //float3 worldRefl = reflect(-viewDir, i.worldNormal);
                float3 worldRefl = myReflect(-viewDir, i.worldNormal);

                // sample the texture
                fixed4 col = texCUBE(_CubeMap, normalize(worldRefl));
                return col;
            }
            ENDCG
        }
    }
}
```

## Refract Cube Map
* 环境反射原理
  * 使用视线关于物体顶点法线的反射向量作为采样的方向向量

* 斯涅尔定律（Snell's law）
  * 入射角的正弦值与折射角的正弦值的比值为一定值，
  * 而此定值跟入射与折射介质有关

* sinθ比值等于介质折射率n的比值

* 使用公式计算
  * float3 R = refract(L, N, RefractRatio)

<br>![image](https://user-images.githubusercontent.com/74708198/224551823-96ff14ef-e9f4-44d4-95ed-2a78ebccd191.png)
<br>![image](https://user-images.githubusercontent.com/74708198/224551834-ef53ee42-87df-4415-96e2-fb1bae70930d.png)

```HLSL
Shader "MyCustom/Chapter9_CubemapRefraction"
{
    Properties
    {
        _CubeMap ("CubeMap",        CUBE) = "" {}
        _RefractRatio ("Refract Ratio", Range(0.1, 1)) = 0.5
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex       : POSITION;
                float3 normal       : NORMAL;
            };

            struct v2f
            {
                float3 worldNormal  : TEXCOORD0;
                float3 worldPos     : TEXCOORD1;
                float4 vertex       : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);

                o.worldNormal = normalize(mul((float3x3)unity_ObjectToWorld, v.normal));
                o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                return o;
            }

            samplerCUBE _CubeMap;
            float _RefractRatio;

            float3 myReflect(float3 i, float3 n)
            {
                return i - 2 * n * dot(i, n);
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos);

                //float3 worldRefl = reflect(-viewDir, i.worldNormal);
                //float3 worldRefl = myReflect(-viewDir, i.worldNormal);
                float3 worldRef = refract(-viewDir, i.worldNormal, _RefractRatio);

                // sample the texture
                fixed4 col = texCUBE(_CubeMap, worldRef);
                return col;
            }
            ENDCG
        }
    }
}
```

## Fresnel Reflection
* 指光到达材质交界处时，一部分被反射，一部分被折射，
  * 视线垂直于表面时，反射较弱，
  * 视线非垂直于表面时，反射越明显

* Schlick 菲涅耳近似等式

$$  F_{Schlick} (v, n) = F_{0} + ( 1.0 - F_{0} ) * (1.0 - (v \cdot n)) * 5  $$ 
  * 其中F<sub>0</sub>是一个反射系数，用于控制菲涅尔反射的强度，v 是视角方向，n 是表面法线

* Empricial 菲涅耳近似等式

$$  F_{Empricial}(v, n) = max(0, min(1.0, bias + scale * (1- (v \cdot n)power))) $$
  
  * 其中，bias, scale 和 power 是控制项

```HLSL
Shader "MyCustom/Chapter9_CubemapFresnel"
{
    Properties
    {
        _CubeMap        ("CubeMap",        CUBE) = "" {}
        _RefractRatio   ("Refract Ratio", Range(0.1, 1)) = 0.5
        _FresnelRatio   ("Fresnel Ratio", Float)         = 0.5
        _FresnelPower   ("Fresnel Power", Float)         = 1.0
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            // make fog work
            #pragma multi_compile_fog

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex       : POSITION;
                float3 normal       : NORMAL;
            };

            struct v2f
            {
                float3 worldNormal  : TEXCOORD0;
                float3 worldPos     : TEXCOORD1;
                float4 vertex       : SV_POSITION;
            };

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);

                o.worldNormal = normalize(mul((float3x3)unity_ObjectToWorld, v.normal));
                o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                return o;
            }

            samplerCUBE _CubeMap;
            float _RefractRatio;
            float _FresnelRatio;
            float _FresnelPower;

            float3 myReflect(float3 i, float3 n)
            {
                return i - 2 * n * dot(i, n);
            }

            fixed4 frag (v2f i) : SV_Target
            {
                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos);

                //float3 worldRefl = reflect(-viewDir, i.worldNormal);
                float3 worldRefl = myReflect(-viewDir, i.worldNormal);
                float3 worldRef = refract(-viewDir, i.worldNormal, _RefractRatio);

                float4 reflectCol = texCUBE(_CubeMap, normalize(worldRefl));
                float4 refractCol = texCUBE(_CubeMap, normalize(worldRef));

                //float fresnelFactor = _FresnelRatio + (1 - _FresnelRatio) * pow(1 - dot(viewDir, i.worldNormal), 5);

                float fresnelFactor = pow(1 - dot(viewDir, i.worldNormal), _FresnelPower);

                // sample the texture
                fixed4 col = fresnelFactor * reflectCol + (1 - fresnelFactor) * refractCol;
                
                return col;
            }
            ENDCG
        }
    }
}
```
