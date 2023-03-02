# 什么是Rendering Pipeline

图形渲染管线：
* 把3D坐标转化为屏幕上的2D坐标
* 把2D坐标转变为实际的有颜色的像素

## 数据处理过程

* 管线接收一组3D坐标，然后把它们转化为你屏幕上的有色2D像素输出
* 管线可以被划分为几个阶段，每个阶段将会把前一个阶段的输出作为输入
* 所有的这些阶段都是高度专门化的（它们都有一个特定的函数），并且很容易并行执行
* 在图形渲染管线中快速处理数据，这些小程序叫着色器（Shader）
  * 着色器有好多种，其中有些允许开发者自己配置，以更细致的控制管线中的特定部分
  * 着色器运行在GPU上
  * Untiy的着色器是自身封装后的一种便于书写的Shader
  
<br> ![image](https://user-images.githubusercontent.com/74708198/222188139-79f96417-ccaa-4d87-b51a-85726c76e451.png)
<br> 注：蓝色部分为可编辑部分。

## 渲染管线总流程

* 我们以数组的形式传递3个3D坐标作为图形渲染管线的输入，这个数组叫做 **顶点数据(Vertex Data)**，是**一系列顶点的集合**
* **顶点着色器(Vertex Shader)** 把顶点的3D坐标转为另一种3D坐标，同时允许我们对顶点属性进行一些处理
* **图元装配(Primitive Assembly)** 将所有的点装配成指定图元的形状
* **几何着色器(Geometry Shader)** 它可以通过产生新顶点构造出新的(或者其它的)图元来生成其他形状，在我们这里它生成了另一个三角形
* **光栅化阶段(Rasterization Stage)会把图形映射为最终屏幕上相应的像素，生成片段(Fragment)，并执行裁剪(Clipping)，丢失超出你试图以外的所有像素从而提升效率
* **片元着色器(Fragment Shader)** 计算每个像素的最终颜色
* **Alpha测试和混合(Blending)** 阶段检测片元的对应深度(和模板(Stencil))值，决定是否丢弃；这个阶段也会检测alpha值并对物体进行混合(Blend)

## Untiy中的渲染管线
<br>![image](https://user-images.githubusercontent.com/74708198/222195038-0b38dbe8-522d-4725-8c7c-051400282bb4.png)
<br>![image](https://user-images.githubusercontent.com/74708198/222195294-37a33db6-ed83-499c-936f-4103c129fd88.png)
<br>![image](https://user-images.githubusercontent.com/74708198/222195348-3260aa47-b3e3-4a8f-b979-4c682d02d9ad.png)
开发者需要考虑流程：

### Untiy提供的渲染管线
* 内置渲染管线(Built-in)
  * Untiy的默认渲染管线
  * 这是通用渲染管线，其自定义选项有限
  * 2018版本之前只有内置这种管线，只有前向渲染路径(Forward Rendering)和延迟渲染路径(Defered Rendering)
  * 前向渲染就是按一特定顺序渲染，所以如果多一盏灯，渲染开销成倍增长。
  * 延迟渲染是先把不透明的物体渲染到缓存中，存储材质信息（颜色，镜面反射，光滑度等），之后再另外一个延迟通道对每个像素进行着色，对于不透明和复杂的着色依然需要额外前向渲染通道，但是对于复杂光源环境不限。但移动设备上对延迟渲染容易显存爆炸
* 通用渲染管线(URP)
  * 一种可快速轻松自定义的可编程渲染管线
  * 允许在各种平台上创建优化的图形
  * 一种快速的单通道的前向渲染，低端中端设备上成本可能低于内置渲染管线
  * URP会根据每个对象来剔除光线，且允许在单个通道中计算其光照。于内置管线相比，可降低调用频次。且包含了2D渲染器和延迟渲染器
* 高清渲染管线(HDRP)
  * 一种可编程渲染管线
  * 可在高端平台上创建出色的高保真图形
  * 比内置渲染管线的光照速度更快，消耗更低
* 可编程渲染管线(SRP)
  * 可以通过Untiy的可编程渲染管线API来创建自己的自定义渲染管线，C#编写，Unity的很好的革新
  * URP和HDRP实际上就是Untiy预选构建好的两个SRP
## GPU中的渲染管线
![image](https://user-images.githubusercontent.com/74708198/222378337-1194a0c8-8000-45c5-87e6-827131e29d87.png)
<br>![image](https://user-images.githubusercontent.com/74708198/222378690-d64eefcd-efb6-4e23-9104-c93c16d897a5.png)
* 几何阶段
  * 图元数据
  * 顶点着色器(输入为单个顶点，输出也是变化后的顶点)
  * 裁剪(会做归一化处理)
  * 曲面细分(可选，实现LOD(Level Or Detail))
  * 几何着色器(可选，输入为一个完整的图元，输出可以是一个或者多个或不输入图元，将输入的点线扩展为多边形)
<br> **Tips：** 顶点着色器除了做局部坐标观察坐标转换以后，会不会做一些光照计算？
<br> **Answer：** 一般来说光照计算都在Fragment shader中，但是也可以在顶点着色器中做光照计算，但是不常见且效果不自然。
<br>![image](https://user-images.githubusercontent.com/74708198/222381625-57013ba0-2285-4c24-ad80-0d2fe241aa34.png)

* 光栅化阶段
  * 图元组装
  * 光栅化
  * 片段着色器(颜色计算，根据法线等等计算光照)/像素着色器
  * 测试和混合阶段(不可编程，可配置)
<br>![image](https://user-images.githubusercontent.com/74708198/222384909-00ec59cc-4158-4077-8693-c9c6d5ab3951.png)

## TEST-Draw a triangle in Untiy
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
public class TriangleDrawer : MonoBehaviour
{
    public float scale = 100f;
    public Vector3 offset;
    public Material mat;
    // Start is called before the first frame update
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    private void OnPostRender()
    {
        DrawTriangle();
    }

    void DrawTriangle()
    {
        Vector3[] vertices = new Vector3[3]
        {
            new Vector3(0.5f,-0.5f,0),
            new Vector3(-0.5f,-0.5f,0),
            new Vector3(0,0.5f,0)
        };

        Vector3 v0 = vertices[0]*scale+offset;
        Vector3 v1 = vertices[1]*scale+offset;
        Vector3 v2 = vertices[2]*scale+offset;
        //调用OpenGL
        GL.PushMatrix();
        mat.SetPass(0);
        GL.LoadOrtho();

        //GL.Begin(GL.TRIANGLES);
        //GL.Vertex3(v0.x/Screen.width, v0.y/Screen.height, 0);
        //GL.Vertex3(v1.x/Screen.width, v1.y/Screen.height, 0);
        //GL.Vertex3(v2.x/Screen.width, v2.y/Screen.height, 0);

        GL.Begin(GL.LINES);
        //V0-V1
        GL.Vertex3(v0.x/Screen.width, v0.y/Screen.height, 0);
        GL.Vertex3(v1.x/Screen.width, v1.y/Screen.height, 0);
        //V1-V2
        GL.Vertex3(v1.x/Screen.width, v1.y/Screen.height, 0);
        GL.Vertex3(v2.x/Screen.width, v2.y/Screen.height, 0);
        //V2-V0
        GL.Vertex3(v2.x/Screen.width, v2.y/Screen.height, 0);
        GL.Vertex3(v0.x/Screen.width, v0.y/Screen.height, 0);

        GL.End();
        GL.PopMatrix();

    }
}
```

### Shape
<br>![image](https://user-images.githubusercontent.com/74708198/222396108-767eb35e-80e0-4c03-ae13-faa654326a4e.png)
### Line
<br>![image](https://user-images.githubusercontent.com/74708198/222396369-3861173a-cc5c-43ce-9b20-787335ccd703.png)
