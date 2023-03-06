# Lambert
## Lambert Working in Vertex Shader
```HLSL
Shader "Unlit/Lambert_Vert"
{
    Properties
    {
        _BaseCol ("BaseCol",COLOR) = (1.0, 1.0, 1.0, 1.0)
        _kD ("Kd", Range(0,1)) = 1.0 
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

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL; //Modele Space
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float4 col : COLOR;
            };

            float4 _BaseCol;
            float _kD;
            uniform float4 _LightColor0; //主光源


            v2f vert (appdata v)
            {
                v2f o;
                // Unity 内置 模型 * 世界 * 投影矩阵(UNITY_MATRIX_MVP)
                o.vertex = UnityObjectToClipPos(v.vertex);
                
                // Normal
                float3 WSnormal = normalize(mul(float4(v.normal, 0.0), unity_ObjectToWorld).xyz);
                //float3 WSnormal = normalize(mul(float4(v.normal, 0.0), unity_WorldToObject).xyz);

                //Light
                float3 WSlight = normalize(_WorldSpaceLightPos0.xyz); //Pos0一般指默认的值

                float Lambert = max(dot(WSnormal,WSlight),0.0);
                float3 Diff = _kD * Lambert *_BaseCol.rgb * _LightColor0.rgb;

                //ambient
                float3 ambient = UNITY_LIGHTMODEL_AMBIENT.rgb; //内置环境光
                float3 finalCol = Diff + ambient;
                o.col = float4(finalCol,0.0);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                
                return i.col;
            }
            ENDCG
        }
    }
}
```
## Lambert Working in Fragment Shader
```HLSL
Shader "Unlit/Lambert_Frag"
{
    Properties
    {
        _BaseCol ("BaseCol",COLOR) = (1.0, 1.0, 1.0, 1.0)
        _kD ("Kd", Range(0,1)) = 1.0 
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

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL; //Modele Space
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float3 WSnormal : TEXCOORD0; //需要用贴图存储
            };

            float4 _BaseCol;
            float _kD;
            uniform float4 _LightColor0; //主光源


            v2f vert (appdata v)
            {
                v2f o;
                // Unity 内置 模型 * 世界 * 投影矩阵(UNITY_MATRIX_MVP)
                o.vertex = UnityObjectToClipPos(v.vertex);
                
                // Normal
                o.WSnormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));

                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                // Normal
                float3 WSnormal = i.WSnormal;

                //Light
                float3 WSlight = normalize(_WorldSpaceLightPos0.xyz); //Pos0一般指默认的值

                float Lambert = max(dot(WSnormal,WSlight), 0.0);
                float3 Diff = _kD * Lambert *_BaseCol.rgb * _LightColor0.rgb;

                //ambient
                float3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz; //内置环境光
                float3 finalCol = Diff + ambient;

                return float4(finalCol, 1.0);
            }
            ENDCG
        }
    }
}
```
## 效果展示
下图中左为顶点着色器中的模型，其光照过度部分明显有锯齿，右为片元着色器中的模型。
<br>![image](https://user-images.githubusercontent.com/74708198/223133576-e7a53c36-dedd-482f-82ae-8a7b0a76a3c4.png)
