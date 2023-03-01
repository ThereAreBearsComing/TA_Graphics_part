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
  * 2018版本之前只有内置这种管线，只有前向渲染路径(Forward Rendering)和延迟渲染路径(Defered Rendering)。
* 通用渲染管线(URP)
  * 一种可快速轻松自定义的可编程渲染管线
  * 允许在各种平台上创建优化的图形
* 高清渲染管线(HDRP)
  * 一种可编程渲染管线
  * 可在高端平台上创建出色的高保真图形
* 可编程渲染管线(SRP)
  * 可以通过Untiy的可编程渲染管线API来创建自己的自定义渲染管线
