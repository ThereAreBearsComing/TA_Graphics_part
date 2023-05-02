# Shader
## 基础知识
* [渲染管线](Pages/0.0TheRenderingPipline.md)
  * CPU / GPU之间的通信
  * `GPU流水线`
  * *拓展课程：可编程的渲染管线*

* [Shader基础](Pages/0.1SurfaceShaders.md)
  * Surface Shader
  * Vertex / Pixel Shader
  * [数据类型和关键词](Pages/0.2Shader中的基础数据类型和关键词.md)
  * *拓展课程：Geometry Shader / Compute Shader*

* [数学基础](Pages/1.0Shader数学基础(常用函数).md)
  * `坐标系`
  * 点，矢量，标量
  * `矩阵以及变换`
  * `坐标空间转化`
  * `法线变化`

## 初级知识
* 多边形着色，标准光照模型
  * 自发光
  * 高光
  * [漫反射](Pages/0.5LambertModule.md)
  * 环境光

* [光照模型](Pages/0.7常见多边形着色算法(Unity平行光点光源自适应函数).md)
  * Gouraud shading
  * Flat shading
  * Phong
  * [线性插值](Pages/1.0.1Lerp.md)

* [切线空间](Pages/0.6法线贴图和切线空间.md)
  * `TBN矩阵构建`
  * `切线空间 / 其他空间（世界空间）互转`

* [Cubemap](Pages/0.8CubeMap(反射贴图).md)
  * 如何实现反射
  * 如何实现折射

* 使用shader绘制图形单元
  * [基础圆形，椭圆形，等等](Pages/)
  * [复杂混合图形，等等](Pages/)
  * *拓展课程：绘制3D图形单元，编写Substance Designer程序化纹理*
  * *拓展课程：运用在特效上的图形使用，Raymarching绘制图形*
 
## 中级知识
* [`PBR，基于物理的渲染`](Pages/1.6PBR简介.md)
  * 漫反射和反射
  * 能量守恒
  * 金属度

* [`Unity PBR`](Pages/1.7.1UnityPBR.md)
  * 金属度工作流
  * 高光工作流
  * [线性空间 / Gamma空间](Pages/1.7.0GammaSpace&LinearSpace.md)

* [`自己实现一个PBR Shader`](Pages/1.8CustomPBR.md)
  * 直接光漫反射
  * 直接光镜面反射
  * 间接光漫反射
  * 间接光镜面反射

* [NPR，非真实感渲染前瞻](Pages/)
  * 基于屏幕特效的风格化渲染
  * `卡通渲染`
  * `日式卡通`
  * *拓展课程：市面上主流卡通渲染解决方案介绍及实现*

* [Alpha](Pages/)
  * Alpha Test
  * [Alpha Blend](Pages/2.2Blending.md)

* [渲染顺序](Pages/0.9缓冲和队列.md)
  * `透明物体渲染顺序`
  * `渲染引擎排序的常用方法`
  * `混合图层`
  
* [`Stencil Buffer`](Pages/2.3StencilBuffer.md)

* [剔除与深度测试](Pages/2.4Culling&Z)

* [渲染路径和光源](Pages/2.5RenderingPass&LightSource.md)
  * `前向渲染路径`
  * 顶点照明渲染路径
  * `延迟渲染路径`
  * *拓展课程：可编程的渲染管线*

* [阴影](Pages/2.6Shadow.md)
  * `平行光`
  * `点光源`
  * `聚光灯`
  * `PCSS, PCF，软阴影的优化方式`

* [屏幕后处理](Pages/2.7PostProcessing.md)

## 渲染方案
* [装液体的容器](Pages/)
  * 考点：透明物体渲染排序
  * 考点：剔除和深度

* [挂满水滴的玻璃](Pages/)
  * 考点：使用shader绘制图形单元的综合应用
  * 考点：屏幕特效

* [模拟海洋平面](Pages/)
  * 考点：使用深度贴图融合海平面上和下的场景
  * 考点：正弦波驱动顶点动画
  * 考点：海水的渲染

* [云层产生的阴影](Pages/)
  * 考点：场景的3维坐标重建在摄像机裁剪面上
  * 考点：UV动画
  
* 草地shader的实现
  * Geometry Shader
  
* 体积云
  * 光线进步实例

* 体积光

* [原神NPR复刻](Pages/原神NPR.md), step by step.



## Shader学习方法总结
* 如何理解Shader
  * Shader不是渲染的全部
  * 学习Shader，更是学习渲染管线

* 如何选择辅导书
  * 入门：《GPU编程与CG语言之阳春白雪下里巴人》,《Cg教程_可编程实时图形权威指南》，《Unity Shader入门精要》
  * 中阶：主要了解其中包括的全局光照、PBR内容（这也是我们课程结束后应该达到的水平）
  * 高阶：《Real-Time Rendering》，Unity3D内建着色器源码剖析

* 如何提高
  * 透彻了解渲染管线
  * 学习OpenGL或者DX，了解图形API
  * 图形算法
  * 数学，物理知识


