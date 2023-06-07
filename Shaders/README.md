# Shader
## 通用基础
* [渲染管线](Pages/0.0TheRenderingPipline.md)
  * CPU / GPU之间的通信
  * `GPU流水线`
  * *拓展课程：可编程的渲染管线*
  * [PC手机图形API介绍](Pages/0.01PC手机图形API介绍.md)
  * [进阶：GPU硬件架构](Pages/)
  * [进阶（优化）：Early-Z 、Z Prepass（Pre-Z）](Pages/0.03Early-Z,Pre-Z.md)
  * [进阶（优化）：前向/延迟渲染管线介绍](Pages/0.02前向&延迟渲染管线介绍.md)

* [数学基础](Pages/1.0Shader数学基础.md)
  * `坐标系`
  * 点，矢量，标量
  * `矩阵以及变换`
  * `坐标空间转化`
  * `法线变化`
  * [MVP矩阵详解](Pages/1.02MVP矩阵.md)
  * [Clip Space & NDC Space，透视除法](Pages/1.03容易混淆的ClipSpacevsNDC透视除法.md)
 
* [模型与材质基础](Pages/0.31模型与材质基础.md)
  * [纹理介绍](Pages/0.32纹理介绍.md)
  * [Bump Map 凹凸映射](Pages/0.33BumpMap凹凸映射.md)
  * [进阶：Flowmap](Pages/0.331Flowmap.md)
  * [进阶（优化）：纹理压缩](Pages/0.34纹理压缩.md)

* 显示效果基础
  * 色彩空间介绍
  * [线性空间 / Gamma空间](Pages/1.7.0GammaSpace&LinearSpace.md)
  * [LDR和HDR](Pages/1.01LDR&HDR.md)

 * [Shader基础](Pages/0.1SurfaceShaders.md)
   * Surface Shader
   * Vertex / Pixel Shader
   * [数据类型和关键词](Pages/0.2Shader中的基础数据类型和关键词.md)
   * [常用函数](Pages/1.00常用函数.md)
   * [Unity常用内置变量和函数](Pages/1.1Built-in常用内置函数.md)
   * [逻辑条件判断，及优化方法](Pages/1.5LogicalStatements.md)
   * *拓展课程：[Geometry Shader](Pages/TessellationShader(TESS)&GeometryShader(GS).md) / [Compute Shader](Pages/6.00ComputerShader.md)*

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

* [实时阴影](Pages/2.6Shadow.md)
  * `平行光`
  * `点光源`
  * `聚光灯`
  * `PCSS, PCF，软阴影的优化方式`
  * 阴影渐变
  * [SSAO 屏幕空间环境光遮蔽](Pages/2.63SSAO.md)

* [屏幕后处理](Pages/2.7PostProcessing.md)
  * 亮度，对比度
  * 边缘检测描边
  * 高斯模糊
  * [Bloom](Pages/2.71Bloom.md)
  * 运动模糊
  * [URP的后处理](Pages/2.72URP后处理.md)

## 进阶部分
* [Local Tonemapping方案总结](https://zhuanlan.zhihu.com/p/519457212)

* 基于物理的大气散射
  * [单次大气散射](Pages/5.00单次大气散射.md)

* [ComputerShader](Pages/6.00ComputerShader.md)
  * [兼容性](Pages/6.01ComputerShader兼容报告.md)

* SPR
  * 


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

* [星穹铁道场景渲染]

* [三月七小房间渲染]

* [原神场景渲染复现](Pages/原神NPR.md), step by step.
  * 层次细节LOD技术
  * PBR渲染方程
  * IBL环境光照
  * 集群延迟光照
  * 实时阴影
  * 环境光遮蔽
  * 体积特效
  * 屏幕空间反射
  * 视差
  * 植被渲染
  * 人物渲染
  * 反向动力学骨骼


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


