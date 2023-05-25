## 性能优化之道
### 性能优化本质
* 慢与快的问题
* 前提：
  * 兼容性：不能因优化导致兼容性变差
  * 稳定性：不能因优化造成稳定性变差
  * 性价比：优化要有度，考虑成本与复杂度

### 性能优化的流程
* 发现问题 (什么平台、什么操作系统、什么情况下出现问题，一般问题还是特例问题等)
* 定位问题 (什么地方造成的性能问题(GPU,CPU,内存？)，我们要用什么工具、什么方法确定瓶颈)
* 研究问题 (确定用什么方案处理这个问题，要考虑性能优化的前提)
* 解决问题 (按问题研究的结论去实际处理，并验证处理结果与预期的一致性)


### 影响性能的四大类问题
* ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/18fd4235-5519-4e82-bdef-8b70996366d2)
  * CPU
    * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/65134847-8ee9-46d1-8990-84001a9bb769)
  * GPU
    * ![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/d2f7aed2-4af2-48f1-a58b-d907f1e270ec)
  * 带宽
  * 内存
  * 隐藏问题：
    * 功耗比
    * 填充率
    * 发热量

### 性能问题可能的情况
* 瓶颈可能性按由高到低的顺序排列 (个人经验总结)
  * CPU利用率
  * 带宽利用率
  * CPU/GPU强制同步
  * 片元着色器指令
  * 几何图形到CPU到GPU的传输
  * 纹理CPU到GPU的传输
  * 顶点着色器指令
  * 几何图形复杂性

* 注：先检查CPU瓶颈，如果不是GPU端直接降低分辨率，如果性能大幅升高则为片元计算过于复杂

### 经常用的优化思路
* 升维与降维
  * **一般升维为了优化性能，但降维则降低性能，但可以帮助理解算法**，例如：多线程并行程序往往比单线程串行更高效，当然前提是并行的优化要大于创建多线程资源的开销。**另外高纬度也可解决一些低纬度无法解决的问题**，例如，变换矩阵的齐次坐标，四元数解决万向节锁的问题

* 维度转换，空间与时间，量纲转换
  * 面向对象的AOS到面向数据的SOA的转化，量纲的转化，积分变换等，这些都可理解为维度的转换。其中以空间换时间是大多数算法优化的方式，如通过缓存池避免每次分配的开销，TAA和DLSS都是通过时间上的卷积来实现优化，例如：你将一张2k大小的图，弄成4帧1k大小的图哪里有省，像素不是一样多嘛？但一旦有时间维度的参与计算后，你上一帧处理过的像素在下一帧不一定会发生变化，这样就无需重新计算，即达成节省



## Unity性能优化流程（Version0.1）
* 静态导入资源优化篇
  * 项目创建与总览
  * [Audio导入设置检查与优化](Pages/0.1Audio导入设置检查与优化.md)
  * [Model导入设置检查与优化](Pages/0.2Model导入设置检查与优化.md)
  * [纹理的基础概念](Pages/0.3纹理的基础概念.md)
  * [纹理导入设置检查与优化](Pages/0.3纹理的基础概念.md)
  * [动画导入设置检查与优化](Pages/0.4动画导入设置检查与优化.md)


* Unity工作流优化篇
  * [工程目录与Assets目录设置](Pages/0.5工程目录与Assets目录设置.md)
  * [资源导入工作流](Pages/0.6资源导入工作流.md)


* 编辑器创建资源优化篇
  * [场景](Pages/0.7场景.md)
  * [预制体](Pages/0.8预制体.md)
  * [UGUI(上)](Pages/0.9UGUI.md)
  * [UGUI(下)](Pages/0.9UGUI.md)
  * [物理](Pages/1.0物理.md)
  * [动画](Pages/1.1动画.md)


* 渲染性能优化篇
  * 初步
    * 性能优化之道
    * [性能总览与瓶颈定位](Pages/1.2性能总览与瓶颈定位.md)
    * [渲染流程分析](Pages/1.3渲染流程分析.md)
    * [SSAO优化](Pages/1.4SSAO优化.md)
    * [AA优化](Pages/1.5AA优化.md)
    * [PostProcess后处理优化](Pages/1.6PostProcess后处理优化.md)
  * 中阶
    * [Unity中的Culling](Pages/1.7Culling.md)
    * Unity中的Simplization
    * Unity中的Batching(上)
    * Unity中的Batching(下)
    * 场景简化总览与远景简化
    * 中景简化与LOD策略
    * 遮挡剔除与光影剔除优化
  * 终篇
    * 渲染流程的精简与优化
    * Terrain地形优化
    * 主光源级联阴影优化
    * Shader指令优化


* 内存篇
  * Unity中的内存概述与工具方法
  * Native内存分配器详解
  * 内存指标术语与进程内存介绍
  * 项目设置与内存优化
  * Shader与托管内存优化


* 发布篇
  * 耗电量与发热量优化
  * 启动时间优化
  * 打包优化与课程总结



# 当我们谈优化时，我们谈些什么
REF：[原文](https://zhuanlan.zhihu.com/p/68158277)

## Intro
减少模型数量，减少/合并draw call，缩减贴图尺寸，压缩贴图，使用LOD”，因为这就是所谓“正确但无用的话”：所有游戏不都是这么优化么？此外，**对于一个项目来讲，模型的面数，贴图尺寸，LOD的级别这些信息往往是在DEMO阶段就已经由TA主导确立的**。

* 一个典型的性能优化的流程，从profile开始，到确定瓶颈，然后针对瓶颈优化，测试优化的效果，再进入下一轮的profile（一个性能的优化有可能会导致新的性能瓶颈产生），如此无限循环
所以，当我们谈论性能优化的时候，我们究竟在谈些什么呢？

理解了这个问题的意图：如果我们换一种问法，比如“**渲染常见的性能瓶颈有哪些？具体可能出现在什么样的情景下？为什么这些情景会造成对应的性能瓶颈？**”会不会是一个更好的问题？

## 说说GPU的架构
核弹厂有一篇关于自家GPU架构和逻辑管线的非常好的文章，如果你想要对GPU的结构有一个比较完整系统的认识，请一定不要错过这篇[Life of a Triangle](https://developer.nvidia.com/content/life-triangle-nvidias-logical-pipeline)。比较可惜的是，这篇文章只更新到Maxwell这代架构。
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/c8b4ca9b-5440-438a-9e04-84d5acf46e80)

这张图是基于数据的流向，对GPU的硬件单元进行了大致的划分，实际上GPU中，最核心的部件可以被分成三大块，原作者画了图来示意他们大致的协作模式：
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/c1ed84c1-b30f-4bae-b756-6270366a43c4)

通常来说，GPU会有三个比较重要的部分，分别是**控制模块，计算模块（图中的GPC）** 和 **输出模块（图中的FBP）**。通常来说，GPU架构的设计需要有**可伸缩性**，这样通过增加/阉割计算和输出模块，就能够产生性能不同的同架构产品（比如GTX1070和GTX1080的主要区别就在于GPC和FBP的数量），以满足不同消费水平和应用场景的需求。

### 控制模块
控制模块负责接收和验证（**主要是Host和Front End**）来自CPU的经过打包的PushBuffer（经过Driver翻译的Command Buffer），然后读取顶点索引（**注意是Vertex Indices不是Vertex Attributes，主要由Primitive Distributor负责**）分发到下游管线或者读取Compute Grid的信息（**主要由CWD负责，这部分是Compute Pipeline，不作展开**）并向下游分发CTA。

Tips：计算管线和图形管线共享大部分的芯片单元，只在分发控制的单元上各自独享（PD和CWD）。许多较新的Desktop GPU允许图形和计算管线并行执行，可以在一些SM压力轻的图形计算环节（比如Shadow Map绘制），利用Compute Shader去做一些SM压力重的工作（比如后处理），让各个硬件单元的负载更加平衡。

### 计算模块
计算模块是GPU中最核心的部件，Shader的计算就发生在这里。早期的硬件设计上，我们会区分VS，PS等Shader类型，并设计专用的硬件单元去执行对应类型的Shader，但这样的方法并不利于计算单元满负荷运转，所以现在所有的GPU设计都是**通用计算单元，为所有Shader类型服务**。在NV的显卡里这个模块全称是**Graphics Processing Cluster**，通常一个GPU会有多个GPC，每个GPC包含一个光栅器（Raster）负责执行光栅化操作，**若干个**核心的计算模块，称之为**Texture Process Cluster（TPC）**，关于TPC，我们进一步分解来看这张大图：
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/92ef7051-5c6f-4125-b6b2-62d579c913b7)

通常来说，一个TPC拥有：
* 若干个用于贴图采样的**纹理采样单元（Texture Units）**
* 一个用于接收上游PD数据的**Primitive Engine**，PE作为一个固定单元，**负责根据PD传来的顶点索引去取相应的顶点属性（Vertex Attribute Fetch），执行顶点属性的插值，顶点剔除**等操作
* 一个负责Shader载入的模块
* 若干执行Shader运算的计算单元，也就是**流处理器（Streaming Multi-Processor，SM， AMD叫CU）**

TPC内最核心的部件就是SM，这里我们再进一步分解SM看这张大图：
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/1191195d-811f-478b-9d45-8e2a2c6fdf09)

一个SM通常拥有一块**专用于缓存Shader指令的L1 Cache**，若干**线程资源调度器**，一个**寄存器池**，一块**可被Compute Pipeline访问的共享内存（Shared Memory），一块专用于贴图缓存的L1 Cache，若干浮点数运算核心（Core），若干超越函数的计算单元（SFU），若干读写单元（Load/Store）**。

作为核心计算单元，GPU的设计思路和CPU有很大的不同，就我所知的体现在两个方面：
* GPU拥有较弱的流程控制（Flow-Control）的能力
* GPU拥有更大的数据读写带宽，并配合有更多样的延迟隐藏技术

