# Shader Properties

```HLSL
Shader "Shaders/GUILearner"
{
    Properties
    {
        // [Attribute]_Name("Display Name", Type) = Defaut Value
        
        _MyColor ("MyColor", Color) = (1,1,1,1)
        [HDR]_MyHDR ("MyHDR", Color) = (1,1,1,1) //Color中的值可以大于1
        _MyInt ("My Int", Int) = 1.0 //Float进行四舍五入
        _MyFloat1("My Float1", Float) = 0.5
        _MyFloat2("My Float2", Range(0, 1)) = 0.5
        [PowerSlider(3)]_MyFloat3("My Float3", Range(0,1)) = 0.5 //非线性滑动
        [IntRange]_MyFloat4("My Float4", Range(0,10)) = 0 //整数显示, 从shader的精度上讲只有伪整型
        [Toggle]_MyFloat5("My Float5", Range(0,10)) = 0 //布尔值，即对错
        [Enum(UnityEngine.Rendering.CullMode)]_MyFloat6("Float6", Float) = 1 //下拉菜单，Off = 0，不剔除，Front = 剔除前面，Back = 剔除后面
        
        _MyVector("MyVector", Vector) = (0,0,0,0)
        
        _MyTexture("MyTexture", 2D) = "White" {} //White,black,gray,bump
        [NoScaleOffset]_MyTexture2("MyTexture2", 2D) = "White" {}  //关闭偏移计算，优化
        [Normal]_MyNormal("MyNormal", 2D) = "" {} // = "gray" {},赋予非法线会报错，点击后帮助转换为NormalMap
        
        _My3DTexture("My3DTexture", 3D) = "" {}
        _MyTexture3 ("MyTexture3", CUBE) = "" {} // 方形天空盒贴图，用于PBR反射
        
        [Header(This is Header)]_MyInt2("MyInt2", Int) = 1 //父标题
        [HideInInspector]_MyInt3 ("MyInt2", Int) = 1
        
        _MyEmission("My Emission", Color) = (0.0 , 1.0, 0.0, 1.0)
        _Glossiness ("Smoothness", Range(0,1)) = 0.5
        _Metallic ("Metallic", Range(0,1)) = 0.0
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
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                UNITY_FOG_COORDS(1)
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                UNITY_TRANSFER_FOG(o,o.vertex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed4 col = tex2D(_MainTex, i.uv);
                // apply fog
                UNITY_APPLY_FOG(i.fogCoord, col);
                return col;
            }
            ENDCG
        }
    }
    CustomEditor "GUIExtension.MyCustomShaderGUI" //在此引用GUI的C#脚本
}
```
GUI C# 脚本
```C#
using UnityEngine;
using UnityEditor;
using System;

namespace GUIExtension
{
    public class MyCustomShaderGUI : ShaderGUI
    {
        bool isColorFoldout;
        bool isIntFoldout;
        bool isFloatFoldout;
        bool isVectorFoldout;
        bool isTextureFoldout;

        public override void OnGUI(MaterialEditor materialEditor, MaterialProperty[] properties)
        {
            // render the default gui
            //base.OnGUI(materialEditor, properties);

            Material targetMat = materialEditor.target as Material;
            Show(materialEditor, properties);

            EditorGUI.BeginChangeCheck();

            if (EditorGUI.EndChangeCheck())
            {
                // TODO
            }
        }

        void Show(MaterialEditor materialEditor, MaterialProperty[] properties)
        {
            GUILayout.Label("My Custom Shader GUI");

            isColorFoldout = EditorGUILayout.BeginFoldoutHeaderGroup(isColorFoldout, "Color Data Type");
            if (isColorFoldout)
            {
                materialEditor.ColorProperty(FindProperty("_MyColor", properties), "My Color");
                materialEditor.ColorProperty(FindProperty("_MyHDR", properties), "My HDR");
            }
            EditorGUILayout.EndFoldoutHeaderGroup();

            isIntFoldout = EditorGUILayout.BeginFoldoutHeaderGroup(isIntFoldout, "Int Data Type");
            if (isIntFoldout)
            {
                materialEditor.IntegerProperty(FindProperty("_MyInt", properties), "My Int");
                materialEditor.IntegerProperty(FindProperty("_MyInt2", properties), "My Int2");
                materialEditor.IntegerProperty(FindProperty("_MyInt3", properties), "My Int3");
            }
            EditorGUILayout.EndFoldoutHeaderGroup();

            isFloatFoldout = EditorGUILayout.BeginFoldoutHeaderGroup(isFloatFoldout, "Float Data Type");
            if (isFloatFoldout)
            {
                materialEditor.FloatProperty(FindProperty("_MyFloat1", properties), "My Float");
                materialEditor.FloatProperty(FindProperty("_MyFloat2", properties), "My Float2");
                materialEditor.FloatProperty(FindProperty("_MyFloat3", properties), "My Float3");
                materialEditor.FloatProperty(FindProperty("_MyFloat4", properties), "My Float4");
                materialEditor.FloatProperty(FindProperty("_MyFloat5", properties), "My Float5");
                materialEditor.FloatProperty(FindProperty("_MyFloat6", properties), "My Float6");
            }
            EditorGUILayout.EndFoldoutHeaderGroup();

            isVectorFoldout = EditorGUILayout.BeginFoldoutHeaderGroup(isVectorFoldout, "Vector Data Type");
            if (isVectorFoldout)
            {
                materialEditor.VectorProperty(FindProperty("_MyVector", properties), "My Vector");
            }
            EditorGUILayout.EndFoldoutHeaderGroup();

            isTextureFoldout = EditorGUILayout.BeginFoldoutHeaderGroup(isTextureFoldout, "Texture Data Type");
            if (isTextureFoldout)
            {
                materialEditor.TexturePropertySingleLine(EditorGUIUtility.TrTextContent("Color", "Color (RGB) and Opacity (A)"), FindProperty("_MyTexture", properties), null);
                //materialEditor.TextureProperty(FindProperty("_MyTexture", properties), "My Texture");
                materialEditor.TextureProperty(FindProperty("_MyTexture2", properties), "My Texture2");
                materialEditor.TextureProperty(FindProperty("_MyTexture3", properties), "My Texture3");
                materialEditor.TextureProperty(FindProperty("_MyNormal", properties), "My Normal");
                materialEditor.TextureProperty(FindProperty("_My3DTexture", properties), "My 3D Texture");
            }
            EditorGUILayout.EndFoldoutHeaderGroup();

            EditorGUILayout.EndFoldoutHeaderGroup();
        }
    }
}
```
然后UI将变为：
<br>![image](https://user-images.githubusercontent.com/74708198/223117340-2427cb7e-1d44-4710-9153-2ca70c5e1c85.png)

