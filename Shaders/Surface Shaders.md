# Shader
![image](https://user-images.githubusercontent.com/74708198/222397698-370a5dc3-606c-4cda-add3-75cc154b3fcf.png)

## Shader语言
* OpenGL的Open Shading Language，即GLSL
* DriectX的High Level Shading Language，即HLSL
* NVIDIA的C for Graphic，即CG

## Shader种类
![image](https://user-images.githubusercontent.com/74708198/222398985-589bf3c6-1eee-4146-afd9-1187bbfbcaac.png)

### Untiy Shader
Untiy自己封装的Shader，直接用就行。
<BR>![image](https://user-images.githubusercontent.com/74708198/222405872-0a995b6a-c902-415e-8c0d-e97509a5a77c.png)

### Unity 创建Shader
非传统意义上的shader，而是shaderLab的一个文件，一个UnityShader中做的远多于传统意义上的shader，一个文件可以即包含顶点着色器又包含片元着色器，也设置渲染设置，例如开启混合剔除等等。
<br>![image](https://user-images.githubusercontent.com/74708198/222406320-5f7bfe18-3e64-4b07-8f29-c6c3791378cb.png)

```CG
Shader "Custom/Surface Shader"
{
    Properties
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MyEmission("My Emission", Color) = (0.0 , 1.0, 0.0, 1.0)
        _Glossiness ("Smoothness", Range(0,1)) = 0.5
        _Metallic ("Metallic", Range(0,1)) = 0.0
    }
    SubShader
    {
        CGPROGRAM
        // 编译指令 + 关键词 + 函数名（自定义）+ 光照模型
        //#pragma surface Mysurf Lambert
        #pragma surface surf Standard

        struct Input
        {
            float2 uv_MainTex; // 对应到Properties的_MainTex Sampler2D的两个值
        };

        fixed4 _Color;
        fixed4 _MyEmission;

        half _Glossiness;
        half _Metallic;


        void surf (Input IN, inout SurfaceOutputStandard o)
        {
            o.Albedo = _Color; // Albedo漫反射
            //o.Emission = _MyEmission;
            // Metallic and smoothness come from slider variables
            o.Metallic = _Metallic;
            o.Smoothness = _Glossiness;

        }
        ENDCG
    }
    FallBack "Diffuse"
}
```
