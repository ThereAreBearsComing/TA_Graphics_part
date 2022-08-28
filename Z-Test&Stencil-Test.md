# 3.1 Z-Test and Stencil-Test
## 深度测试和模板测试

## 1.模板测试
### 1.3 原理
![1661617262883](https://user-images.githubusercontent.com/74708198/187038923-86a2611a-58f5-40ec-ad6f-fab96867caf7.jpg)

* 左图为颜色缓冲区中的一张图，在模板缓冲区中我们会给这张图的每一个片元分配一个0-255的数字（8位，默认为0）
* 中、右图可以看到，我们修改了一些0为1，通过自定义的一些准则，如输出模板缓冲区中1对应的片元的颜色；0的不输出，最后通过模板测试的结果就如右图所示

### 1.2 实现效果

![1661618012807](https://user-images.githubusercontent.com/74708198/187039418-87de13ea-4c8f-40de-bd33-5cc7a591c4f4.jpg)
* 传送门效果：可以看到左边传送门内的景象正是右侧的场景
<br> ![5c9cb924-18cc-4a37-8b97-f7588d37b5e6](https://user-images.githubusercontent.com/74708198/187039714-d5835bd3-1afc-4dcc-b556-679ae98aed25.gif)
![53e15f7f-b23d-4419-9826-213d970fa98e](https://user-images.githubusercontent.com/74708198/187039720-40ef7871-f5ea-45eb-ab44-08aa4e98a9fc.gif)
![1e61de9e-b6cb-4d2d-8adf-fae25294bb5d](https://user-images.githubusercontent.com/74708198/187039725-a1e8625b-07a5-46b2-93c8-d219db41e202.gif)
![5d362e7f-17ea-41ae-b6c5-c581964ae0b4](https://user-images.githubusercontent.com/74708198/187039727-126ab970-3621-45e7-8e14-bf9a8947d1de.gif)

* Minions讲解的一些效果，例如3D卡牌效果、侦探镜效果等
<br> ![06771cb9-3d27-4b38-b8ce-f39ded2744ff](https://user-images.githubusercontent.com/74708198/187039590-e9838049-f69a-4412-908f-0eba07d2404b.gif)
![3003f31b-996c-4965-92fe-2e5ed29cf3a8](https://user-images.githubusercontent.com/74708198/187039595-ee1aced1-6ca8-47cd-9e85-9afb3e0b0d22.gif)
![e35936e9-c26f-4827-a81c-5fb6d95fd5fd](https://user-images.githubusercontent.com/74708198/187039597-69ca0404-6094-4a38-addc-cbe9e1e03db7.gif)
![3607f252-5625-40db-82c4-a51c53c6e2a7](https://user-images.githubusercontent.com/74708198/187039600-b4297960-490d-4145-a8ff-f08cb6ac6381.gif)

* 每个正方体面显示不同场景（每个面作为蒙版来显示场景）
### 1.3 理解
* 对于上述的例子总结一下，这些效果基本可以归结为三层组成
  * 以 **1.2** 中的传送门为例子，三层分别对应：门外场景、门内场景、门
* 也就是说可以理解为：包括两层物体/场景、和一层遮罩

## 2.什么是模板测试
### 2.1 从渲染管线理解
* 下图为从片元着色器到FrameBuffer的流程（逐片元操作）
<br>![1661621587660](https://user-images.githubusercontent.com/74708198/187041495-db4d3722-41ce-40e7-9990-000c22a0e9c0.jpg)

* 逐片元操作流程：
<br>![1661621612911](https://user-images.githubusercontent.com/74708198/187041508-b4311103-e705-4fd5-a04f-508a2aeff8b3.jpg)

* 可以看到逐片元的流程依次为
  * 像素所有权测试→裁剪测试→透明度测试→模板测试→深度测试→透明度混合
  * PixelOwnershipTest：
    * 简单来说就是控制当前屏幕像素的使用权限
    * e.g.：游戏引擎仅渲染游戏窗口
  * ScissorTest（裁剪测试）：
    * 在渲染窗口再定义要渲染哪一部分
    * 和裁剪空间一起理解，也就是只渲染能看到的部分
    * e.g.只渲染窗口的左下角部分
  * AlphaTest（透明度测试）
    * 提前设置一个透明度预值
    * 只能实现不透明效果和全透明效果
    * e.g. 设置透明度a为0.5，如果片元大于这个值就通过测试，如果小于0.5就剔除掉
  * StencilTest（模板测试）
  * DepthTest（深度测试）
  * Blending（透明度混合）
    * 可以实现半透明效果
  * 完成接下来的其他一系列操作后，我们会将合格的片元/像素输出到帧缓冲区（FrameBuffer）

* 逐片元操作是可以配置但不可编程的（对应图中为黄色背景），也就是说是由管线/硬件自身规定好的，我们只能对里边的内容进行配置。

### 2.2 从逻辑上讲
```
if ( referenceValue & readMask comparsionFuction stencilBufferValue & readMask):
pass pixels

else:

abandon them
```
  * 理解：
    * referenceValue：当前模板缓冲片元的参考值
    * stencilBufferValue：模板缓冲区里的值
    * 中间的comparisonFunction，就是做一个比较
  * 结果：
    * 如果通过，这个片元就进入下一个阶段
    * 未通过/抛弃，停止并且不会进入下一个阶段，也就是说不会进入颜色缓冲区
  * 总结：就是通过一定条件来判断这个片元/片元属性执行保留还是抛弃的操作
### 2.3 从书面概念上理解
模板缓冲区-FrameBuffer
* 模板缓冲区可以为屏幕上的每一个像素点保存一个无符号整数值（通常为8位int 0-255）。
* 这个值的意义根据程序的具体应用而定。
模板测试
* 渲染过程中，可以用这个值与预先设定好的参考值作（ReferenceValue）比较，根据结果来决定是否更新相应的像素点的颜色值。
* 这个比较的过程就称为**模板测试**。
* 模板测试在**透明度测试之后，深度测试之前**。
* 如果模板测试通过，相应的像素点更新，否则不更新。

## 3. 基本原理和使用方法
### 3.1语法表示/结构解释
```HLSL
stencil{
    Ref referenceValue
    ReadMask readMask
    WriteMask writeMask
    Comp comparsionFunction
    Pass stencilOperation
    Fail stencilOperation
    ZFail stencilOperation
}
```
    * Ref：当前片元的参考值（0-255）
    * ReadMask：读掩码
    * WriteMask：写掩码
    * Comp：比较操作函数
    * Pass：测试通过，之后进行操作（StencilOperation，后边有详细讲解）
    * Fail：测试未通过，也会进行一个操作
    * ZFail：模板测试通过，深度测试未通过

### 3.2 ComparisonFunction
* 我们可以根据需求配置
<br>![1661697170841](https://user-images.githubusercontent.com/74708198/187079376-1ab833c1-6df0-4daa-a48a-fa5af23a309d.png)

### 3.3 StencilOperation 更新值
* 有不同的更新操作，根据自己的需求进行配置
<br>![1661697248449](https://user-images.githubusercontent.com/74708198/187079425-b7e6d487-5371-4771-88b9-54641fcfd0a8.png)

## 4. Demo效果展示和讲解
### 4.1 案例一：3D卡牌效果
* **注：**Unity中模板缓冲区默认都是0
* 在材质中将ReferenceValue改为0时的效果（模型直接摆放的效果）
<br>![image](https://user-images.githubusercontent.com/74708198/187079677-c77ad2c2-47fb-4237-af57-01f12c424cba.png)

#### 4.1.1 蒙版的shader
```HLSL
Shader "FX/StencilMask"{
    Properties{
      
        _ID("Mask ID", Int) = 1
    }

        SubShader{
            Tags{ "RenderType" "Opaque" "Queue" = "Geometry+1" }
            ColorMask O //RGBA、 RGB、R、 G、 B、 e
            ZWrite off
            Stencil{// input stencil
                Ref[_ID]
                Comp always //#ilalways
                Pass replace //Milkeep
                //Fail Keep
                //Fail keep
            }
        Pass{
            CGINCLUDE
        struct appdata {
            float4 vertex : POSITION;
        };
        struct v2f {
            float4 pos : SV POSITION;
        };
        v2f vert (appdata v) {
            v2f o;
            o.pos = UnityObjectToClipPos(v.vertex);
            return o;
        }
        half4 frag(v2f i) : SV_Targetf

            return half4(1,1,1,1);
        }
            ENDCG
    }
}
}
```
代码理解
* 只有一个int类型的属性，作为蒙版的ID
* 渲染类型为不透明物体，队列为Geometry+1（默认的不透明物体后进行蒙版的渲染）
* ColorMask  颜色遮罩，0就是什么都不输出（是高度可配置的，可以改为RGBA（没有遮罩）、输出单通道R、G、 B）
* ZWrite off  关闭深度写入，防止显示的东西被深度剔除（后边深度测试细讲）
* Stencil/模板测试部分
  * Ref [_ID] 索引值就是前边属性声明的ID
  * Comp always  //默认比较
  * Pass replace //默认是keep
  * Fail、ZFail，不写的话都是默认值，如代码所示
* 后续就是顶点、片元着色器
* 颜色给一个half4的就行，因为前边已经ColorMask0了（什么都不输出）

#### 4.1.2 物体的shader
```HLSL
Shader "Toon/Lit StencilMask"{
    Properties {
        _Color ("Main Color", Color) = (0.5,0.5,0.5,1)
        _MainTex ("Base (RGB)", 2D) = "white" (}
        _Ramp ("Toon Ramp (RGB)", 2D) = "gray" {}
        _ID ("Mask ID", Int) = 1
    }
    
    SubShader{
        //render after the Mask, the queue is higher than Mask
        Tags { "RenderType"-"Opaque" "Queue" "Geometry+2"}
        LOD 200
        //ColorMask A
        Stencil ( //input Stencil
            Ref [ ID] // the _ID match the Mask
            Comp equal

CGPROGRAM
#pragma surface surf ToonRamp
sampler2D _Ramp;

// custom Lighting function that uses a texture ramp based
// on angle between Light direction and normal
#pragma lighting ToonRamp exclude path: forward
inline half4 LightingToonRamp (SurfaceOutput s, half3 lightDir, half atten)
{
#ifndef USING DIRECTIONAL_LIGHT
lightDir = normalize (lightDir);
#endif

half d = dot (s.Normal, lightDir)*0.5 + 0.5;
half3 ramp = tex2D (_Ramp, float2(d,d)).rgb;

half4 c;
c.rgb = s.Albedo * _LightColor@.rgb * ramp * (atten * 2);
c.a = 0:
return c;
}

sampler2D MainTex;
float4 Color;

struct Input {
    float2 uv MainTex : TEXCOORDO;
};

void surf (Input IN, inout SurfaceOutput o) {
    half4 c = tex2D(_MainTex, IN.uv MainTex) * _Color;
    o.Albedo = c.rgb;
    o.Alpha = c.a;
}
ENDCG

    }

    Fallback "Diffuse"
}
```
代码理解：
* 除了自身的属性之外，同样给了一个ID
* 渲染类型为不透明物体，队列为Geometry+2（前边蒙版后渲染）
* Stencil/模板测试部分：
  * Ref [_ID] 同上
  * Comp equal ，当给定的索引值和当前模板缓冲区的值相等时，才会渲染这个片元。
  * 后续不写就是默认值
* 后边就是自身的光照模型、颜色渲染等

#### 4.1.3 总结实现思路
* 前提认识：
  * Unity的模板缓冲区的默认值是0
* 默认物体（mask外边的物体）的索引值也设为0，Comp设为always
  * 也就是比较的结果是一直通过的，且保持保持模板缓冲区的值不变（不进行模板测试操作的物体渲染完之后，模板缓冲区的值还是0）
* 然后开始渲染蒙版（mask）
  ○ mask的设置Comp也是always，但是不同的是：ID给了1（属性部分定义），并且Pass的设置为replace，也就是说mask所在的模板缓冲区的值变成了1。
* 到这里，总结一下，mask外的物体 值是0，mask的值是1
* 最后是mask里边的物体。
  * mask的模板测试是这样的：ID是1，Comp是equal。翻译一下就是：模板缓冲区的值为1，比较的条件是相等
  * 此时，里边物体的模板缓冲区的值是1，外边物体的模板缓冲区是0，Comp的条件是相等。结果很明显不相等，这样的效果就是：除mask显示的部分，外边的场景不渲染。
  * 回顾前边，我们mask的缓冲区的值也为1，通过了测试，所以mask部分（卡牌部分）渲染了出来。

### 4.2 案例二：盒子不同面显示不同场景
<br>![1661700116537](https://user-images.githubusercontent.com/74708198/187081591-621298ff-b1b1-4622-b170-3bb565a0f8a0.png)
**实现思路**
* 和卡牌效果类似，一个用蒙版遮罩的物体，盒子每个面使用一个蒙版遮罩
* 同样利用默认的值为0来做，只是面多了，蒙版和里边显示的物体也多了，ID依次为1、2、3、4
* 总结：一个蒙版对应一个物体，他们使用相同的ID，出来的效果就是：每个面显示的盒子内部物理不同

<br>**属性和模板测试部分代码截图**
```HLSL

    Properties
    {
        _Color ("Main Color", Color) = (1,1,1,1)
    
        _SRef ("Stencil Ref", Float) = 1
        [Enum(UnityEngine.Rendering.CompareFunction)]  _SComp ("Stencil Comp", Float) = 8
        [Enum(UnityEngine.Rendering.Stencil0p)] _SOp("Stencil Op", Float) = 2
    }
    
    SubShader
    {
        Tags{ "Queue" = "Geometry+1" }
        
        ZWrite off
        ColorMask 0
    
        Stencil
        {
            Ref[_SRef]
            Comp[_SComp]
            Pass[_SOp]
        }
```
代码理解：
* 属性中使用了一个内置的枚举，这样就可以在外边自己选择可配置的属性了
## 5. 模板测试的总结
* 最重要(用来比较的）两个值：
  * 当前模板缓冲区值（StencilBufferValue）、模板参考值（ReferenceValue）
* 模板测试的内容：
  * 主要就是对这两个值进行特定的比较操作，例如Never、Always、Equal等，具体参考上文的表格
* 模板测试后
  * 要对模板缓冲区的值进行更新操作，例如Keep，Replace等，具体参考上文表格
  * 更新操作：可以根据不同的结果对模板缓冲区做不同的更新操作，例如模板测试成功的操作Pass、模板测试失败的操作Fail、深度测试失败的操作ZFail、还有正对正面和背面更新操作Passback，Passfront，Failback等...

## 6.扩展及参考资料
### 6.1 扩展用法
* 描边操作
<br>![image](https://user-images.githubusercontent.com/74708198/187082002-c0c44fb9-8b1d-422a-a018-2fba628e79cc.png)
* 多边形填充
<br>![image](https://user-images.githubusercontent.com/74708198/187082007-94fd2630-7065-4b5c-bdc8-06551ffc39f9.png)
* 反射区域控制
<br>![image](https://user-images.githubusercontent.com/74708198/187082016-9c5a1e8d-3909-4392-8fd0-9e3dff51021f.png)
* shadow volume阴影渲染
<br>![image](https://user-images.githubusercontent.com/74708198/187082022-910a8aa0-9709-4bd1-b2f7-c0bb504850a2.png)

### 6.2 参考资料
* 官方文档：https://docs.unity3d.com/Manual/SL-Stencil.html
* https://blog.csdn.net/u011047171/article/details/46928463
* https://blog.csdn.net/liu_if_else/article/details/86316361
* https://gameinstitute.qq.com/community/detail/127404
* https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/02%20Stencil%20testing/
* https://www.patreon.com/posts/14832618
* https://www.udemy.com/course/unity-shaders/

## 深度测试部分
## 1. 什么是深度测试
* 帮助我们处理物体的遮挡关系
### 1.1 从渲染管线理解
![image](https://user-images.githubusercontent.com/74708198/187082259-e8b651df-1b80-4771-a112-1bbb060d5f92.png)
* 深度测试同样位于逐片元操作过程中，在**模板测试之后，透明度混合之前**。

### 1.2 从逻辑上理解
```HLSL
if (ZWrite On && (correntDepthValue ComparisonFunction DepthBufferValue)){
     写入深度
}else{
     忽略深度
}

if (correntDepthValue ComparisonFunction DepthBufferValue){
     写入颜色缓冲
}else{
     不写入颜色缓冲
}
```
* 理解：
  * 和模板测试差不多，都是通过一个比较来判断一系列操作
  * 图1：
    * 如果ZWrite On，且当前深度值和深度缓冲区的值作比较，如果通过就写入深度，不通过就忽略深度
  * 图2：
    * 当前深度值和深度缓冲区中的值做比较，如果通过就写入颜色缓冲区，不通过就不写入颜色缓冲区
### 1.3 从书面概念上理解
* 深度测试的概念
  * 就是针对当前屏幕上（更准确的说是FrameBuffer）对应的像素点，将对象自身的深度值与当前深度缓冲区的深度值做比较，如果通过了，这个对象在该像素点才会将颜色写入颜色缓冲区

### 1.4 从发展上看
![image](https://user-images.githubusercontent.com/74708198/187083098-379c8cee-e6fe-4bd4-93a2-7c214766dd68.png)
* 我们要渲染一个场景的话，通常会有多个物体。
* 首先要控制渲染顺序
  * 画家算法：
    * 这里是指油画的画法，也就是画一幅油画，是从远处开始画，然后近处的东西一点点叠加在上面（GAMES系列的课提到过多次）
    * 存在的问题：例如一列物体，最前面的物体最大，站在正前面看只能看到最前面的物体，这样一来后边的就不用画了，不然就是性能浪费（OverDraw）。
  * Z-Buffer算法：
    * 通过深度缓冲区来控制渲染顺序
* 控制Z-Buffer对深度的存储
  * 例如：什么时候更新深度缓冲区、什么时候使用深度缓冲区
  * 两个典型的功能：
    * Z Test
    * Z Write
  * 具体后边会讲
* 控制不同类型物体的渲染顺序
  * 透明物体
  * 不透明物体
  * 渲染队列（很有用的概念，后边会讲）
* 减少OverDraw
  * Early-Z，一种优化手段，后边会讲
    * Z-cull（优化手段）
    * Z-check（确认正确遮挡关系）
## 2. 基本原理和使用方法 
### 2.1 Z-Buffer（深度缓冲区）
* 和颜色缓冲区一样，在每个片段中存储了信息，并且通常和颜色缓冲有着一样的宽度和高度
* 颜色缓冲区：
  * 就是最终在显示屏硬件上显示颜色的GPU显存区域了，这个缓冲区储存了每帧更新后的最终颜色值，图形流水线经过一系列测试，包括片段丢弃、颜色混合等，最终生成的像素颜色值就储存在这里，然后提交给显示硬件显示。
* 深度缓冲是由窗口系统自动创建的，它会以16、24、32位float形式存储深度值。
  * //大部分系统中深度值是24位的
* Z-Buffer中存储的是当前的深度信息，对于每个像素存储一个深度值。
* 我们可以通过Z-Write 、Z-Test来调用Z-Buffer，来达到想要的渲染效果
### 2.2 Z Writer（深度写入）
* 深度写入包括两种状态
  * ZWrite On  、 ZWrite Off
  * 当我们开启深度写入，物体被渲染时针对物体在屏幕（FrameBuffer）上每个像素的深度都写入到深度缓冲区。
  * 关闭深度写入状态，物体的深度就不会写入深度缓冲区。
* 除了ZWrite的是否写入深度缓冲区，更重要的是：是否通过深度测试，也就是Z-Test
  * 如果Z-Test都没通过，也就不会写入深度了。
  * 也就是说，只有ZTest和ZWrite都可行的情况下才写入深度缓冲区
* 综上，ZWrite有On、Off两种情况；ZTest有通过、不通过两种情况，两者结合的四种情况如下：
![image](https://user-images.githubusercontent.com/74708198/187083419-0cc0147c-da06-4670-a5a4-6131df62f000.png)

### 2.3 Z-Test的比较操作
![image](https://user-images.githubusercontent.com/74708198/187083436-0bcd0113-c81b-455d-bf63-8ec2bc491bf8.png)
● 默认情况下：
  ○ Z Write：On
  ○ Z Test：LEqual
● 深度缓冲一开始为无穷大
### 2.4 Unity的渲染队列
* Unity内置的几种渲染队列：
![image](https://user-images.githubusercontent.com/74708198/187083669-ef2a8496-83da-4bda-a66f-6e31345f53a3.png)
  * 按照渲染顺序从先到后排序，队列数越小，越先渲染；反之同理。
* Unity中设置渲染队列：
  * 语法：Tags { “Queue” = “渲染队列名”}
  * 默认是Geometry
* Unity中不透明物体的渲染顺序：从前往后
  * 也就是说深度小的先渲染，其次再渲染深度大的
* Unity中透明物体的渲染顺序：从后往前（类似画家算法，会造成OverDraw）
* 可以在shader的Inspector面板中查看渲染队列相关属性
![image](https://user-images.githubusercontent.com/74708198/187083706-0312f754-944d-4afb-af2b-159620ad5150.png)
### 2.5 简述Early-Z技术
* Early-Z是位于三角形遍历之后、逐片元操作之前的。
* 传统的渲染管线中，ZTest是在Blending阶段，这时进行深度测试的话，所以对象的像素着色器都会计算一遍，没有性能提升，只是为了得到正确的效果，造成了大量的无用计算。（深度测试失败的片元是已经经过计算的片元，也就是说：到在一步测试不通过而被抛弃，前边的计算就是无用功了）
* 为了减少这些不必要的计算，现代GPU运用了Early-Z技术，在顶点和片元阶段之间（光栅化之后，片元着色器之前）进行一次深度测试（如下左图黑框部分）。
  * 如果这次深度测试失败，那就不用在片元着色器中作无关紧要的计算了，这样一来就会带来性能提升。
  * 最终的ZTest仍然要进行，以保证正确的遮挡关系。
  * 如右图前一次的Z-Cull是为了裁剪达到性能优化的目的，后一次的Z-check是为了保证正确的遮挡关系。
  * ![image](https://user-images.githubusercontent.com/74708198/187083730-c29587d3-4933-4533-b25e-66a06b9f4178.png)
### 2.6.深度值
<br>##正确的理解深度值的概念##
* 首先先了解一下模型在渲染管线中的几次空间变换
![image](https://user-images.githubusercontent.com/74708198/187083765-f8f4560f-0867-492e-aaf2-e4d959ddc60c.png)
  * 模型一开始所在的模型空间：**无深度**。
  * 通过M矩阵变换到世界空间，此时模型坐标已经变换到了齐次坐标（x，y，z，w）：**深度存在z分量**。
  * 通过V矩阵变换到观察空间（摄像机空间）：**深度存在z分量（线性）**
  * 通过P矩阵变换到裁剪空间：**深度缓冲中此空间的z/w中（已经变成了非线性的深度）**
  * 最后通过一些投影映射变换到屏幕空间 
<br>**为什么深度缓冲区中要存储一个非线性的深度？**
<br>详细链接
  ○ https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/01%20Depth%20testing/
  <br>原因1：给近处更多的精度
* 在深度缓冲区中的深度值是介于 0.0~1.0之间的，从观察者看到的内容与场景中所有对象的z值作比较。
* 这些z值可以投影平截头体（就是视锥）的近平面和远平面之间的任何值。
* //补充：
  * **平截头体**：又称视景体、视锥，是三维世界中在屏幕上可见的区域，即虚拟摄像机的视野
  * 下图中红框的位置是平截头体，就是摄像机拍摄的范围。
  * 贴上维基百科：https://zh.wikipedia.org/wiki/%E8%A7%86%E4%BD%93
  * ![image](https://user-images.githubusercontent.com/74708198/187083884-bec1b528-82a1-4fff-9895-d45b5a364f9c.png)
* 要转换这些视图空间的z值到[0，1]范围内，方法之一就是线性转换，具体如下图
![image](https://user-images.githubusercontent.com/74708198/187083895-6eab6d76-e5cd-4892-afab-5dc4da9a9351.png)
* 然而实践中几乎不使用线性深度缓冲区，正确的投影特性的非线性深度方程是和1/z成正比的。这样一来会有如下效果：`在Z很近的时候有高精度，Z很远的时候低精度`，这符合我们生活中的情况，具体如下图
  * `其实可以回想前几节课中伽马校正部分对比理解一下，也是根据实际情况（人眼特性），给暗部更多的精度，这里是近处给更多精度`。
  * ![image](https://user-images.githubusercontent.com/74708198/187083969-5d924c60-ddd2-4686-9850-9c3ad7373748.png)
<br>另一个原因：Z-Fight-深度冲突
  * 当两个平面或三角形紧密相互平行的时候，深度缓冲区不具有足够的精度来确定哪一个考前。
  * 结果就是这两个形状不断切换顺序，导致怪异问题，看起来像是两个形状在争夺靠前的位置。
  * 这就被称为深度冲突。
  * 深度冲突是深度缓冲区的普遍问题，当对象的距离越远一般越强（因为深度缓冲区在z值非常大的时候没有很高的精度）
  * 深度冲突无法避免，但是有技巧可以防止出现：
    * 物理上的做法，就是让物体不要靠得太近。
    * 尽可能的把近平面设置的远一点。
    * 放弃一些性能来得到更高精度的深度值。
## 5.Demo效果展示和讲解
### 5.1 案例一：三个正方体遮挡关系
![image](https://user-images.githubusercontent.com/74708198/187084023-7c9eb04f-dc7f-4fbf-951a-d1892403e6f6.png)
* 场景中有三个正方体，并赋予了不同的颜色。正常的情况应该是从前到后依次为蓝、绿、红
<br> ** 图1详解：正常渲染顺序 **
![image](https://user-images.githubusercontent.com/74708198/187084046-d88b3a03-e99c-4912-990e-43b62ed92c7a.png)
* 梳理渲染过程：
  * 没渲染时，此时Unity的深度缓冲区默认值为无穷大
  * 渲染蓝色正方体
    * ![image](https://user-images.githubusercontent.com/74708198/187084070-7c837a09-f245-474a-861f-970b90a1cff5.png)
    * 相对于默认深度缓冲区的无穷大，肯定是小于等于，所以测试通过
  * 渲染绿色正方体
    * 此时蓝色物体位置的深度缓冲区的值已经不是无穷大了，其它位置还是
    * 注：深度缓冲区和颜色缓冲区都是相对于片元来讲的（片元可以理解为未完成的一个像素，还处于渲染管线中的像素）
    * 绿色正方体进行深度测试，深度测试同样是LessEqual，并且绿色的深度值比蓝色正方体的大。
    * 结果就是：两个正方体重叠部分是大于深度缓冲区的，也就是测试不通过，所以重叠部分没有写入绿色，还是蓝色的
    * 没有重叠部分，深度当然比无穷大小，所以写入， 渲染出来了绿色正方体未重叠的部分。
  * 红色同理。

<br> **图2详解：关闭前排正方体的深度写入**
* ![image](https://user-images.githubusercontent.com/74708198/187084291-053ae211-c362-4a9c-a147-f1933c3db372.png)
* 梳理渲染过程：
  * 设置：将蓝色正方体的深度写入ZWrite 关掉了；
  * 思路：第一个蓝色正方体的渲染时，测试通过，但是并没有写入深度。
  * 也就是说，渲染完蓝色正方体时，深度缓冲区的值还是无穷大。
  * 这就是蓝绿重叠部分，显示绿色的原因。
<br> **图3详解：**
*  相较于图2，只是把绿色正方体的ZTest改为了always
* 无论是LessEqual还是always，测试都通过，所以效果和图2一样
<br> **图4详解：改变ZTest条件**
* ![image](https://user-images.githubusercontent.com/74708198/187084302-9681d135-7ac4-4119-8852-febe8a55709b.png)
* 将红色正方体的ZTest也改为了always，这样一来红色正方体的深度测试也是一直通过，并且写入。
* 因为是从前往后渲染的，所有依次为蓝、绿、红，深度缓冲区中的值也是后边渲染的
* 可以理解为后边遮住前边的效果。
<br> **图5详解：改变渲染队列**
* ![image](https://user-images.githubusercontent.com/74708198/187084306-0b10e9ea-1022-419f-a5c8-ba70ce7e6e5a.png)
* 相对于图4，改变了绿色正方体的渲染队列为Geometry+1
* 此时的帧缓冲区面板如下
  * ![image](https://user-images.githubusercontent.com/74708198/187084312-44efea4c-9829-42c9-a5f9-e2c784d2c228.png)
  * 尽管场景中绿色正方体在红色正方体前面，但是因为队列+1，它的渲染顺序变为了红色正方体后
* `也就是说，渲染队列优先级 > 透明物体的渲染顺序（从前到后）`
<br> **图6详解：再次理解ZTest条件**
* ![image](https://user-images.githubusercontent.com/74708198/187084326-eaf9d1ab-eea9-4437-b8d6-4c28e9eeede2.png)
* 相对于图1，将绿色正方体的ZTest改为了Greater，
* 也就是说蓝色正方体和绿色正方体重叠部分，大于模板缓冲区的部分通过测试，写入模板缓冲区
* 结果就是重叠部分为绿色，而未重叠部分的深度当然小于无穷大，所以没通过测试，自然也就不渲染。
* 红色部分正常。

<br> **shader code**
//重点看深度部分即可（图1），后边就是一个颜色而已
```HLSL
Shader "ZTest/ZTest"{
    Properties
    {
        [HideInInspector]_MainTex ("Texture", 2D) = "white" (}
        _MainColor ("Color", Color) = (1,1,1,1)
        [Enum(Off,0, On,1) ]_ZWriteMode ("ZWrite Mode", Float) = 1
        [Enum(UnityEngine.Rendering.CompareFunction)] _ZComp ("ZTest Comp", Float) = 4
    }
    SubShader
    {
    Pass
    {
        Tags { "RenderType"="Opaque" "Queue" = "Geometry"}
        write [_ZWriteMode]
        Zest [_ZComp]
        Cull Off
        CGPROGRAM
        #pragma vertex vert
        #pragma fragment frag
        // make fog work
        #pragma multi_compile fog
        #include "UnityCG.cginc"
        struct appdata
        {
        float4 vertex : POSITION;
        float2 uv : TEXCOORDO;
        };
        struct v2f
        float2 uv : TEXCOORDO;
        UNITY_FOG_COORDS(1)
        float4 vertex : SV_POSITION;
        };
        
        sampler2D _MainTex;
        float4 _MainTex_ST;
        float4 _MainColor;
        
        v2f vert (appdata v)
        {
            v2f o;
            o.vertex = UnityObjectToClipPos(v.vertex);
            o.uv = TRANSFORM_TEX(v.uv,_MainTex);
            UNITY TRANSFER_FOG(o,o.vertex);
            return o;
        }

        fixed4 frag (v2f i) : SV_Target
        {
            half4 col =_MainColor;
            // apply fog
            UNITY_APPLY_FOG(i.fogCoord, col);
            return col:
        }
        ENDCG
   }
}
```
### 5.2 案例二：X-Ray效果
![image](https://user-images.githubusercontent.com/74708198/187090105-9eb985b6-cee7-48de-afb6-b451e572919b.png)

* 实现思路：
  * 分为三部分：前边的墙、被墙挡住的X-Ray效果部分、高出墙部分的物体
  * 回想一下前边6张图，哪张图是前边渲染完，后边渲染显示在先渲染完前边的？  --->图6
  * 也就是说，X-Ray效果部分我们使用到了ZTest ：Greater，深度写入关闭
  * 高出墙体部分是默认的渲染：LessEqual、ZWrite On

## shader code ##
```HLSL
Shader "ZTest/XRay"

{
    Properties
    {
        _MainTex("Base 2D", 2D) = "white" {}
        _XRayColor ("XRay Color", Color) = (1,1,1,1)
    }
    SubShader
    {
        CGINCLUDE // 使用CGINCLUDE写好vertex、fragment 着色器，逐Pass使用
        #include "UnityCG.cginc"
        fixed4 _XRayColor;
        
        struct v2f
        {
        float4 pos : SV_POSITION;
        float3 normal : NORMAL;
        float3 viewDir : TEXCOORDO;
        fixed4 clr : COLOR;
        };
        
        v2f vertrXray(appdate_base v)
        {
            v2f o;
            o.pos = UnityObjectToCilpPos(v.vertex);
            o.viewDir = ObjSpaceViewDir(v.vertex); //
            o.normal = v.normal;
            
            floate3 normal = normalize(v.normal);
            floate3 viewDir = normalize(v.viewDir);
            float rim = 1 - dot(normal, viewDir);
            
            o.clr = _XRayColor * rim;
            return o;
         }
         
         fixed4 fragray(v2f i) : SV_TARGET
         {
             return i.clr;
         }
         
         sampler2D _MainTex;
         float4 _MainTex_ST;
         
         struct v2f2
         {
         float4 pos : SV_POSITION;
         float2 uv  : TEXCOORDO;
         };
        
         v2f2 vertNormal (appdata_base v)
         {
              v2f2 o;
              o.pos = Unity0bjectToClipPos (v.vertex); 
              o. uv = TRANSFORM TEX(v.texcoord, _MainTex);
              return o;
              }

              fixed4 fragNormal (v2f2 i) : SV_TARGET
              {
              return tex2D(_MainTex, i.uv);
         }
         ENDCG

         Pass // xRay 绘制
         {
              Tags{ "RenderType" = "Transparent" "Queue" = "Transparent"}
              Blend SrcAlpha One
              Zest Greater ///核心
              write Off
              Cull Back

              CGPROGRAM
              #pragma vertex vertXray
              #pragma fragment fragXray
              ENOCG
          }

          Pass // 正常绘制
          {
          Tags{ "RenderType" = "Opaque" }
          Zest LEqual
          ZWrite On

          CGPROGRAM
          #pragma vertex vertNormal
          #pragma fragment fragNormal
          ENDCG
          }
     }
}
```
* 代码理解
  * 写CGINCLUDE的好处：将顶点和片元着色器写在里边，在多passshade的时候，直接调用就可以了。（跟C++头文件类似）
  * X-Ray绘制部分
    * 和之前实现思路相同，ZWrite Off，ZTest Greater
    * Cull back 是剔除背面，为了优化
    * Blend     SrcAlpha One ：由于有一个透明的效果，除了上边的，还需要一步Blend，来做透明度混合
    * 渲染类型和渲染队列为Transparent
  * 正常绘制部分略

### 5.2 粒子系统中的深度测试
* 创建一个粒子系统ParticleSystem，可以看到默认的是透明的
<br>![image](https://user-images.githubusercontent.com/74708198/187090209-95180c61-3412-442e-a721-c16b3b934e94.png)
* 为了加深理解，我们自己来复刻一下这个粒子系统的效果
  * 我们自己创建一个材质，给到粒子上
  * 此时粒子系统变成了这样
<br> ![image](https://user-images.githubusercontent.com/74708198/187090229-6d8c0675-745c-424b-9546-a843fab54b74.png)
* 创建一个shader（Unlit），把粒子的贴图选上，附到材质上，效果如下（是不透明的），这显然不是我们要的效果
<br>![image](https://user-images.githubusercontent.com/74708198/187090236-d783c1a0-e256-4215-ab8e-12b29f146537.png)
* 打开shader修改代码
  * 首先回顾前边说的：Unity中默认的ZWrite On、ZTest是LessEqual、渲染队列是Geometry
  * 我们想要让粒子透明，就需要做如下配置
    * 渲染队列改为透明物体的渲染队列：Transparent
    * ZWrite Off，对于透明物体，是有相互叠加关系的，所以关掉写入
    * ZTest 默认（LessEqual），对于透明物体是这样的：如果透明物体前有不透明物体，此时 透明物体看不到；如果透明物体后面有不透明物体，此时透明物体可以看到。
    * 要渲染透明物体，还要进行Blend操作：Blend One One（加法混合，叠加效果的显示）
  * 修改完成后效果如下：（正是我们想要的效果）
<br>![image](https://user-images.githubusercontent.com/74708198/187090257-1834ded1-faf8-4479-baaf-1bd6e9ae5cf2.png)

## 6. 深度测试的总结
* 最重要的两个值：当前深度缓冲区的值（ZBufferValue） 和 深度参考值（ReferenceValue）。通过比较操作还实现理想的渲染效果
* **Unity中的渲染顺序：**
  * 先渲染不透明物体（从前到后），再渲染透明物体（从后往前）
* **Unity中的默认条件：**
  * ZWrite：On
  * Ztest：LessEqual
  * 渲染队列：Geometry（2000）
* 通过对ZWrite和ZTest的相互组合配置来控制半透明物体的渲染（关闭深度写入，快开启深度测试，透明度混合）
* 引入Early-Z之后深度测试相关的内容（Z-Cull、Z-Check）
* 深度缓冲区中存储的深度值为[0，1]的非线性值

## 7. 扩展及参考资料
### 7.1 深度测试的扩展用法
* 基于深度的着色
  * 如：湖水的效果
  * ![image](https://user-images.githubusercontent.com/74708198/187091238-4fec0a0c-2fe9-4cba-937e-2f16cc09a676.png)

* 阴影贴图（shadowmap）
  * 比较摄像机空间和灯光空间的深度值得到阴影范围
* 透明物体、粒子渲染
* 透视X-Ray效果
* 切边效果
### 7.2 参考资料
* https://blog.csdn.net/puppet_master/article/details/53900568
* https://learnopengl-cn.readthedocs.io/zh/latest/04%20Advanced%20OpenGL/01%20Depth%20testing/
* https://docs.unity3d.com/cn/2018.4/Manual/SL-CullAndDepth.html
* https://blog.csdn.net/yangxuan0261/article/details/79725466
* https://roystan.net/articles/toon-water.html
* 《shader入门精要》
* 《Unity ShaderLab 开发实战详解》



