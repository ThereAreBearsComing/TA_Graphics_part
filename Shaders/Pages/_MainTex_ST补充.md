## 关于Unity中的_MainTex_ST的补充
之前一直有疑问是 ：
  * **float4 _MainTex_ST** 中的_MainTex_ST变量也没有用到，为啥非要声明一下？
  * TRANSFORM_TEX是做什么的

<br>简单来说，TRANSFORM_TEX主要作用是拿顶点的uv去和材质球的tiling和offset作运算， 确保材质球里的缩放和偏移设置是正确的。 （v.texcoord/v.uv 就是顶点的uv）
<br>所以下面这两个函数是等价
```HLSL
o.uv =   TRANSFORM_TEX(v.texcoord,_MainTex);

o.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
```


<br> 简单来说 **_MainTex_ST** 的ST意思为 SamplerTexture 的意思，就是声明 _MainTex 是一张采样图，也就是会进行UV运算。所以如果没有这句话，是不能进行 TRANSFORM_TEX 的运算的。_MainTex_ST.xy为 下图中的Tiling, zw则为下图中的offset (如果Tiling 和Offset你留的是默认值，即Tiling为（1，1） Offset为（0，0）的时候，可以不用)
<br>换成 o.uv = v.texcoord.xy/o.uv = v.uv 也是能正常显示的；相当于Tiling 为（1，1）Offset为（0，0），但是如下图自己填的Tiling值和Offset值就不起作用了








