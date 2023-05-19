# 曲面细分和几何着色器
## 曲面细分着色器（Tessellation Shader）
* 作用
  * 使用过建模软件的应该知道，有曲面细分这个工具，可以将尖锐的物体通过细化变得柔和

* 优点
  * 如果直接使用顶点数更多的模型来实现细化模型的效果，会带来更高的性能消耗
  * 它可以根据自定义的规则来动态调整模型的复杂度，达成对应的效果

* 应用
  * 海浪，雪地，雪地里出现的脚印等，将一条直线进行细分，无限逼近这条曲线
  * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/367cd784-f98a-406b-8163-477ee8399f5a)
  * 与置换贴图结合，置换贴图是移动了顶点位置来实现的，那么通过使用曲面细分着色器，我们可以增多模型顶点数量，真正改变物体形状，使边缘有非常强的凹凸感，从而使得效果看起来更好

## 几何着色器（Geometry Shader）
* 作用
  * 可以将顶点变换为完全不同的图元，并且还能生成比原来更多的顶点

* 优点
  * 可以修改网格（增删改模型的顶点及三角面等，所以可以实现非常多酷炫的效果

* 应用
  * 几何动画
  * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/a2a65f92-fe14-4d4f-b2e0-f44ef25f0db7)![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/9f0a4677-c07b-4482-b4c2-e7c2d0d4fc4c)
  * 草地等（与曲面细分着色器结合），从而动态调整密度
  * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/4db108b7-8ac4-425a-9994-d119733d08ea)

## 着色器的执行顺序
顶点着色器 ————> 曲面细分着色器 ————> 几何着色器 ————> 片元着色器
<br>**如图，可以进一步细分曲面细分着色器，按顺序分为 Hull Shader（细分控制着色器），Tessellation Primitive Generation，Domain Shader（细分计算着色器）**
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/d5e424e9-2a16-45c4-b78e-cd4b0253f3dd)
* **Hull Shader 细分控制着色器 和 Domain Shader 细分计算着色器是可编程的**
* **Tessellation Primitive Generator不可编程**
    * 在OpenGL中HullShader 和DomainShader分别被简称：TCS和TES
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/09c20a6f-2b20-4754-9103-ea0401b0f427)

## TESS曲面细分工作原理
### 输入与输出
* 输入：也叫Patch，可以看成多个顶点的集合，包含每个顶点的属性，可以指定一个Patch包含的顶点数以及自己的属性
* 输出：细分之后的顶点
* 功能：将图元细分（可以是三角形，矩形等）

### 流程
* HULL Shader
  * 决定细分的数量（设定曲面细分因素【Tessellation factor】以及内曲面细分因素【Inside Tessellation factor】）
  * 对输入的Patch参数进行改变（根据需求要进行变换时）
* Tessellation Primitive Generation
  * 进行细分操作
* Domain Shader
  * 对细分后的顶点进行处理，将其从重心空间转换到屏幕空间

### 关于HULL Shader各参数解析
* **Tessellation Factor**
  * 决定将一条边分成几个部分,它又三种分法
    * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/6d55f247-911f-445b-9596-6a5ec00f98f8)
  * 区别：
    * **equa_Spacing:** 将一条边等分，Subdivide参数是几就是几等分；
    * **fractional_even_Spacing:** 最小值为2，Subdivide参数向上取最近的偶数，将周长分为n-2的等长的部分，以及两端不等长的部分，目的是让细分更平滑；
    * **fractional_odd_Spacing:** 最小值为1，Subdivide参数向上取最近的奇数，将周长分为n-2的等长的部分，以及两端不等长的部分，目的是让细分更平滑；
* **Inner Tessellation Factor**
  * 内部细分因素，当该参数为3时，无论上面的Tessellation Factor怎样去进行切分的，我们把三角形切分为三等分，然后分别找最近的两个切分的点，做其延长线，其焦点便是在新内部三角形的一个点；
（概括下就是取边上点的垂线的延长线做交点，直至最后无交点或者交于中心一点）
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/36fe2bb9-d70c-4011-b044-0b1458273e05)

## 曲面细分Demo演示
将一个Cube细分（在线框模式下观察）
```HLSL
Shader "Shader/TessShader"
{
    Properties
    {
        _TessellationUniform("TessellationUniform",Range(1,64)) = 1
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100
        Pass
        {
            CGPROGRAM
            //先定义hullshader和domainshader的名称
            #pragma hull hullProgram
            #pragma domain ds
           
            #pragma vertex tessvert
            #pragma fragment frag

            #include "UnityCG.cginc"
                
            //引入曲面细分的头文件应用对应的函数库
            #include "Tessellation.cginc" 

            #pragma target 5.0
            
            struct VertexInput
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
                float3 normal : NORMAL;
                float4 tangent : TANGENT;
            };

            struct VertexOutput
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
                float3 normal : NORMAL;
                float4 tangent : TANGENT;
            };

            VertexOutput vert (VertexInput v)
            //这个函数应用在domain函数中，用来空间转换的函数，并非顶点着色器的函数，与以往不同
            {
                VertexOutput o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = v.uv;
                o.tangent = v.tangent;
                o.normal = v.normal;
                return o;
            }

            //有些硬件不支持曲面细分着色器，定义了该宏就能够在不支持的硬件上不会变粉，也不会报错
            #ifdef UNITY_CAN_COMPILE_TESSELLATION
                
                //顶点着色器结构的定义
                struct TessVertex{
                    float4 vertex : INTERNALTESSPOS;
                    float3 normal : NORMAL;
                    float4 tangent : TANGENT;
                    float2 uv : TEXCOORD0;
                };

                struct OutputPatchConstant { 
                    //不同的图元，该结构会有所不同
                    //该部分用于Hull Shader里面
                    //定义了patch的属性
                    //Tessellation Factor和Inner Tessellation Factor
                    float edge[3] : SV_TESSFACTOR;
                    float inside  : SV_INSIDETESSFACTOR;
                };

                TessVertex tessvert (VertexInput v){
                    //顶点着色器函数
                    TessVertex o;
                    o.vertex  = v.vertex;
                    o.normal  = v.normal;
                    o.tangent = v.tangent;
                    o.uv      = v.uv;
                    return o;
                }

                float _TessellationUniform;
                OutputPatchConstant hsconst (InputPatch<TessVertex,3> patch){
                    //定义曲面细分的参数
                    OutputPatchConstant o;
                    o.edge[0] = _TessellationUniform;
                    o.edge[1] = _TessellationUniform;
                    o.edge[2] = _TessellationUniform;
                    o.inside  = _TessellationUniform;
                    return o;
                }

                [UNITY_domain("tri")]//确定图元，quad,triangle等
                [UNITY_partitioning("fractional_odd")]//拆分edge的规则，equal_spacing,fractional_odd,fractional_even
                [UNITY_outputtopology("triangle_cw")]
                [UNITY_patchconstantfunc("hsconst")]//一个patch一共有三个点，但是这三个点都共用这个函数
                [UNITY_outputcontrolpoints(3)]      //不同的图元会对应不同的控制点
              
                TessVertex hullProgram (InputPatch<TessVertex,3> patch,uint id : SV_OutputControlPointID){
                    //定义hullshaderV函数
                    return patch[id];
                }

                [UNITY_domain("tri")]//同样需要定义图元
                VertexOutput ds (OutputPatchConstant tessFactors, const OutputPatch<TessVertex,3>patch,float3 bary :SV_DOMAINLOCATION)
                //bary:重心坐标
                {
                    VertexInput v;
                    v.vertex = patch[0].vertex*bary.x + patch[1].vertex*bary.y + patch[2].vertex*bary.z;
			        v.tangent = patch[0].tangent*bary.x + patch[1].tangent*bary.y + patch[2].tangent*bary.z;
			        v.normal = patch[0].normal*bary.x + patch[1].normal*bary.y + patch[2].normal*bary.z;
			        v.uv = patch[0].uv*bary.x + patch[1].uv*bary.y + patch[2].uv*bary.z;

                    VertexOutput o = vert (v);
                    return o;
                }
            #endif

            float4 frag (VertexOutput i) : SV_Target
            {

                return float4(1.0,1.0,1.0,1.0);
            }
            ENDCG
        }
    }
    Fallback "Diffuse"
}
```
## GS几何着色器的工作原理
### 输入与输出
* 输入和输出：都是图元，可以定义输出的顶点数
* 功能：修改网格或者顶点数量
### 流程
* geomerty Shader ：用于编写几何着色器
* maxvertexcount：用于定义最大输出点
* 定义图元输入：point、 line、 lineadj、 triangle、 triangleadj 分别为点线面
* 定义图元输出：PointStream、 LineStream、 TriangleStream 同上

## 几何着色器Demo演示
[几何着色器细节讲解](https://blog.csdn.net/qq_37925032/article/details/82936769)
```HLSL
Shader "Shader Forge/GsTest" {
	Properties
	{
		_MainTex("Texture", 2D) = "white" {}
		_Length("Length", Range(0.01, 10)) = 0.02
	}
		SubShader
		{
			Tags { "RenderType" = "Opaque" }
			LOD 100

			Pass
			{
			Cull Off
				CGPROGRAM
				#pragma target 4.0
				#pragma vertex vert
				#pragma fragment frag
				#pragma geometry geom

				#include "UnityCG.cginc"

				struct appdata
				{
					float4 vertex : POSITION;
					float2 uv : TEXCOORD0;
					float3 normal : NORMAL;
				};

				struct v2g
				{
					float4 vertex : POSITION;
					float2 uv : TEXCOORD0;
				};

				struct g2f
				{
					float2 uv : TEXCOORD0;
					float4 vertex : SV_POSITION;
				};

				sampler2D _MainTex;
				float4 _MainTex_ST;
				float _Length;

				v2g vert(appdata_base v)
				{
					v2g o;
					o.vertex = v.vertex;
					o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
					return o;
				}

			#define ADD_VERT(v) \
	        o.vertex = UnityObjectToClipPos(v); \
	        tristream.Append(o);

			#define ADD_TRI(p0, p1, p2) \
	        ADD_VERT(p0) ADD_VERT(p1) \
	        ADD_VERT(p2) \
	        tristream.RestartStrip();



			[maxvertexcount(21)]
			void geom(triangle v2g IN[3], inout TriangleStream<g2f> tristream)
			{
				g2f o;

				float3 edgeA = IN[1].vertex - IN[0].vertex;
				float3 edgeB = IN[2].vertex - IN[0].vertex;
				float3 normalFace = normalize(cross(edgeA, edgeB));

				float2 centerTex = (IN[0].uv + IN[1].uv + IN[2].uv) / 3;

				o.uv = centerTex;

				float3 v0 = IN[0].vertex;
				float3 v1 = IN[1].vertex;
				float3 v2 = IN[2].vertex;
				float3 v3 = IN[0].vertex + normalFace * _Length;
				float3 v4 = IN[1].vertex + normalFace * _Length;
				float3 v5 = IN[2].vertex + normalFace * _Length;

				ADD_TRI(v0,v3,v2);
				ADD_TRI(v3,v5,v2);
				ADD_TRI(v3,v4,v0);
				ADD_TRI(v4,v1,v0);
				ADD_TRI(v5,v2,v1);
				ADD_TRI(v5,v4,v1);
				ADD_TRI(v3,v4,v5);
			}


				fixed4 frag(g2f i) : SV_Target
				{
					fixed4 col = tex2D(_MainTex, i.uv);
					return col;
				}
				ENDCG
			}
		}
}
```
![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/fa84087a-79f1-4f07-98d6-f60c2b7c6886)

## 利用曲面细分着色器和几何着色器实现效果
用曲面细分控制草的生成数，用几何着色器生成草的形状
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/0231c88b-570b-4a06-943b-876c53f67965)
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/14dca894-afe3-4555-a45d-059f15c93a86)
```HLSL
Shader "Shader Forge/GrassShader" {
	Properties
	{
		_MainTex("颜色图", 2D) = "white" {}
		_SplatMap("分布图",2D) = "white" {}
		_BladeHeightRandom("随机高度系数",float) = 1.0
		_BladeHeight("基础高度",float) = 1.0
		_BladeWidthRandom("随机宽度系数",float) = 1.0
		_BladeWidth("基础宽度",float) = 1.0
		_TopColor("上部颜色",color) = (1.0,1.0,1.0,1.0)
		_BottomColor("下部颜色",color) = (1.0,1.0,1.0,1.0)
			_TessellationUniform("TessellationUniform",Range(1,64)) = 1
	}
		SubShader
		{
			Tags { "RenderType" = "Opaque" }
			LOD 100

			Pass
			{
				CGPROGRAM
				#pragma vertex tessvert
				#pragma fragment frag
				#pragma hull hullProgram
				#pragma domain ds
				#pragma geometry geo

				#include "UnityCG.cginc"
				#include "Tessellation.cginc" 

				#pragma target 5.0


				sampler2D _MainTex;
				float4 _MainTex_ST;
				sampler2D _SplatMap;
				float4 _SplatMap_ST;
				float _BladeHeightRandom;
				float _BladeHeight;
				float _BladeWidthRandom;
				float _BladeWidth;
				fixed4 _TopColor;
				fixed4 _BottomColor;

			  float3x3 AngleAxis3x3(float angle, float3 axis)
			  {
				float s, c;
				sincos(angle, s, c);
				float x = axis.x;
				float y = axis.y;
				float z = axis.z;
				return float3x3(
					x * x + (y * y + z * z) * c, x * y * (1 - c) - z * s, x * z * (1 - c) - y * s,
					x * y * (1 - c) + z * s, y * y + (x * x + z * z) * c, y * z * (1 - c) - x * s,
					x * z * (1 - c) - y * s, y * z * (1 - c) + x * s, z * z + (x * x + y * y) * c
					);
			   }
			   float rand(float3 seed)
			   {
				 float f = sin(dot(seed, float3(4.258, 178.31, 63.59)));
				 f = frac(f * 43785.5453123);
				 return f;
				}

				struct appdata
				{
					float4 vertex : POSITION;
					float2 uv : TEXCOORD0;
					float3 normal : NORMAL;
					float4 tangent :TANGENT;
				};

				struct v2f
				{
					float2 uv : TEXCOORD0;
					float4 vertex : SV_POSITION;
					float3 normal : NORMAL;
					float4 tangent :TANGENT;
				};

				struct geometryOutput
				{
					float4 pos:SV_POSITION;
					float2 uv : TEXCOORD0;
				};

				v2f vert(appdata v)
					//这个函数应用在domain函数中，用来空间转换的函数，并非顶点着色器的函数，与以往不同
				{
					v2f o;
					o.vertex = v.vertex;
					o.uv = v.uv;
					o.tangent = v.tangent;
					o.normal = v.normal;
					return o;
				}

				//有些硬件不支持曲面细分着色器，定义了该宏就能够在不支持的硬件上不会变粉，也不会报错
				#ifdef UNITY_CAN_COMPILE_TESSELLATION

				//顶点着色器结构的定义
				struct TessVertex {
					float4 vertex : INTERNALTESSPOS;
					float3 normal : NORMAL;
					float4 tangent : TANGENT;
					float2 uv : TEXCOORD0;
				};

				struct OutputPatchConstant {
					//不同的图元，该结构会有所不同
					//该部分用于Hull Shader里面
					//定义了patch的属性
					//Tessellation Factor和Inner Tessellation Factor
					float edge[3] : SV_TESSFACTOR;
					float inside : SV_INSIDETESSFACTOR;
				};

				TessVertex tessvert(appdata v) {
					//顶点着色器函数
					TessVertex o;
					o.vertex = v.vertex;
					o.normal = v.normal;
					o.tangent = v.tangent;
					o.uv = v.uv;
					return o;
				}

				float _TessellationUniform;
				OutputPatchConstant hsconst(InputPatch<TessVertex, 3> patch) {
					//定义曲面细分的参数
					OutputPatchConstant o;
					o.edge[0] = _TessellationUniform;
					o.edge[1] = _TessellationUniform;
					o.edge[2] = _TessellationUniform;
					o.inside = _TessellationUniform;
					return o;
				}

				[UNITY_domain("tri")]//确定图元，quad,triangle等
				[UNITY_partitioning("fractional_odd")]//拆分edge的规则，equal_spacing,fractional_odd,fractional_even
				[UNITY_outputtopology("triangle_cw")]
				[UNITY_patchconstantfunc("hsconst")]//一个patch一共有三个点，但是这三个点都共用这个函数
				[UNITY_outputcontrolpoints(3)]      //不同的图元会对应不同的控制点

				TessVertex hullProgram(InputPatch<TessVertex, 3> patch, uint id : SV_OutputControlPointID) {
					//定义hullshaderV函数
					return patch[id];
				}

				[UNITY_domain("tri")]//同样需要定义图元
				v2f ds(OutputPatchConstant tessFactors, const OutputPatch<TessVertex, 3>patch, float3 bary :SV_DOMAINLOCATION)
					//bary:重心坐标
				{
					appdata v;
					v.vertex = patch[0].vertex * bary.x + patch[1].vertex * bary.y + patch[2].vertex * bary.z;
					v.tangent = patch[0].tangent * bary.x + patch[1].tangent * bary.y + patch[2].tangent * bary.z;
					v.normal = patch[0].normal * bary.x + patch[1].normal * bary.y + patch[2].normal * bary.z;
					v.uv = patch[0].uv * bary.x + patch[1].uv * bary.y + patch[2].uv * bary.z;

					v2f o = vert(v);
					return o;
				}
				#endif

				 geometryOutput CreateGeoOutput(float3 pos,float2 uv) {
					geometryOutput o;
					float4 poin = float4(pos.x - 0.5,pos.y,pos.z - 0.5,1);//这里是为了修正了下草生成的中心点
					o.pos = UnityObjectToClipPos(pos);
					o.uv = uv;
					return o;
				 }


				 [maxvertexcount(3)]
				 void geo(triangle v2f IN[3]:SV_POSITION,inout TriangleStream<geometryOutput>triStream) {
					 float3 pos = IN[0].vertex;
					 float2 uv = IN[0].uv;
					 float3 vNormal = IN[0].normal;
					 float4 vTangent = IN[0].tangent;
					 float3 vBinormal = cross(vNormal,vTangent) * vTangent.w;

					 float4 spl = tex2Dlod(_SplatMap,float4(uv,1.0,1.0));//读取一张遮罩图用来表现草地分布
					 float height = ((rand(pos.xyz)) * _BladeHeightRandom + _BladeHeight) * spl.g;
					 float width = ((rand(pos.xyz)) * _BladeWidthRandom + _BladeWidth) * spl.g;

					 float3x3 facingRotationMatrix = AngleAxis3x3(rand(pos) * UNITY_TWO_PI,float3(0,0,1));
					 float3x3 TBN = float3x3(
						vTangent.x, vBinormal.x, vNormal.x,
						vTangent.y, vBinormal.y, vNormal.y,
						vTangent.z, vBinormal.z, vNormal.z
					 );

					float3x3 transformationMat = mul(TBN,facingRotationMatrix);
					geometryOutput o;
					triStream.Append(CreateGeoOutput(pos + mul(transformationMat,float3(width,0,0)),float2(0,0)));
					triStream.Append(CreateGeoOutput(pos + mul(transformationMat,float3(-width,0,0)),float2(1,0)));
					triStream.Append(CreateGeoOutput(pos + mul(transformationMat,float3(0 + sin(_Time.x * 10) * 0.2,0,height)),float2(0.5,1)));
				 }


				fixed4 frag(geometryOutput i) : SV_Target
				{
					fixed4 color = lerp(_BottomColor, _TopColor, i.uv.y);
					return color;
				}
				ENDCG
			}
			Pass
				{
					Tags{"LightMode" = "ForwardBase"}
					Cull Off
					CGPROGRAM
					#pragma target 4.0
					#pragma vertex vert
					#pragma fragment frag

					sampler2D _MainTex;

			   struct appdata
				{
					float4 vertex : POSITION;
					float4 normal : NORMAL;
					float3 tangent: TANGENT;
					float2 uv : TEXCOORD0;
				};

				struct v2f
				{
					float2 uv : TEXCOORD0;
					float4 pos : SV_POSITION;
				};

				v2f vert(appdata v)
				{
					v2f o;
					o.pos = UnityObjectToClipPos(v.vertex);
					float3 viewNormal = mul((float3x3)UNITY_MATRIX_IT_MV, v.tangent.xyz);
					o.uv = v.uv;
					return o;
				}


					fixed4 frag(v2f i) : SV_Target
					{

						 fixed4 col = tex2D(_MainTex, i.uv);
						 return col;
					}
					ENDCG
				}
		}
}
```


