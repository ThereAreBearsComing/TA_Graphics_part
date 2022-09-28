# 理解插值函数lerp

## Lerp函数是针对 **CG/HLSL（一种Shader语法）** 中的lerp函数
它的函数签名和定义:
<br>![image](https://user-images.githubusercontent.com/74708198/192804056-4572a5e3-b94a-4a55-9c59-bc350771a055.png)
<br>从定义来看还是挺简单的，我们主要是理解它有什么作用。另外我们规定，weight是一个在区间[0,1]的实数，倒不是因为取更大的值之后，这个函数就无定义了，而是因为取更大的值，这个函数就失去了我们构造它的理由，另外，CG会限制weight的值在0-1的范围内，超过这个范围会被留在边界0或者边界1

<br>这里的y<sub>1</sub>被称为起点，而y<sub>2</sub>被称为终点，`lerp函数就是取值y1到y2中间的一个值`。取多少呢？**就由weight来控制**，eg.当weight为0.5时，它正好落在起点和终点的中间。为了更加方便理解，我们可以把这个公式换成这种格式:
<br>![image](https://user-images.githubusercontent.com/74708198/192806113-80febc07-7f37-4e27-b1d6-ad1fc958fa8b.png)
<br>简单来说，lerp函数就是在y1和y2之间过渡，唯一不同的地方就是，`y1和y2可以是一个值，也可以是一个函数`。比如，我们可以在正弦函数和线性函数之前做过渡，我们先看一下正弦函数:
<br>![image](https://user-images.githubusercontent.com/74708198/192806750-b5e5deb2-0851-4608-86aa-ceb7923a8be2.png)
<br>再看一下最简单的线性函数（恒等映射函数y=x）
<br>![image](https://user-images.githubusercontent.com/74708198/192806893-fda1a89e-208a-49da-8f3f-f6d450721928.png)
<br>在它俩之间做过渡，我们只需要写出lerp(sin(x),x,0.5)即可。当然，可以调整weight参数观察不同的结果。
<br>**weight为0.5时：**
<br>![image](https://user-images.githubusercontent.com/74708198/192807040-7beb12ec-b7d4-42ed-862f-afa3415d9f3e.png)
<br>**weight为0.8时：**
<br>![image](https://user-images.githubusercontent.com/74708198/192807311-e571c7ed-3713-4162-9cd6-f68a911ad85a.png)
<br>**weight为0.2时：**
<br>![image](https://user-images.githubusercontent.com/74708198/192807336-2aa71cc2-f2a3-4969-98ff-f8caa1f3dad6.png)
<br>是不是非常的直观，所以这就扯到了Lerp函数的两种不同的作用。
* 构造新的函数
* 在两个值之间进行过度
<br>我们可以通过Lerp函数来编写一个简单的Shader测试一下。
```Python
Shader "Assets/MyLab/Shaders/Lerp_tester"{
    Properties{
        _BaseColor1("First Color",Color) = (1.0,1.0,1.0,1.0)
        _BaseColor2("Second Color",Color) = (1.0,1.0,1.0,1.0)
        _Weight("Lerp Weight",Range(0,1)) = 0.5
    }

    SubShader{
        pass{
            Tags{"LightMode"="ForwardBase"}
            CGPROGRAM
            #pragma vertex Vertex
            #pragma fragment Pixel
            #include "Lighting.cginc"

            struct vertexOutput{

                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
            };

            fixed4 _BaseColor1;
            fixed4 _BaseColor2;
            fixed _Weight;

            vertexOutput Vertex(appdata_base v){

                vertexOutput o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.worldNormal = UnityObjectToWorldNormal(v.normal);
                o.worldPos = mul(unity_ObjectToWorld,v.vertex).xyz;

                return o;
            }

            fixed4 Pixel(vertexOutput i):SV_TARGET{

                fixed3 lightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
                fixed3 worldNormal = normalize(i.worldNormal);

                fixed3 albedo = lerp(_BaseColor1.xyz,_BaseColor2.xyz,_Weight);
                //在颜色1和颜色2之间以weight过度

                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                fixed3 diffuse = _LightColor0.xyz * albedo * saturate(dot(worldNormal,lightDir));

                return fixed4(ambient + diffuse,1.0);
            }

            ENDCG
        }
    }
}
```
<br>它的最终效果如下，我们选择两种比较深的颜色来看一下。
<br>![Lerp](https://user-images.githubusercontent.com/74708198/192814682-7f1b7936-449e-4c74-9a88-0ba7941ba1b0.gif)
