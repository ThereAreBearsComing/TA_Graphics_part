# TA 职责和技能介绍
TA基础

## Intro
![image](https://user-images.githubusercontent.com/74708198/200616393-fed5ee20-427b-4697-b5cc-56f06e53b836.png)
<br>要对使用引擎十分了解，eg.当使用Ureal的可破坏网格时，就知道这个工具有很多Bug，而了解如何使用这些工具并且规避Bug，就是有用的经验。

## Shaders
![image](https://user-images.githubusercontent.com/74708198/200617570-ed9175d4-04b7-4d56-9f53-a54c70fcc80a.png)

<br>Work with Texture Artists.
* Developing shaders(Code or node-based)
* Non-standard materials,
* Animated materials
* Difficult cases (e.g. glass)
* Post process

<br>开发着色器并非那么简单，你需要明白技术限制带来的性能损耗。例如，一个简单的着色器里需要放多少材质样本，特别是当没有标准材质可使用时，就会需要用到复杂材质，比如1高级冰面材质，或者熔岩，皮肤和动态材质这类非标准材质，因为现在很多动作也可以直接在着色器中实现，比如各种霓虹灯，全息投影等效果。当然还有很多非常复杂的材质，例如结霜额草皮等，还需要修改引擎，从引擎给予支持。所以我们在实现各种效果时应充分考虑各种限制因素，毕竟不论如何现在的硬件依旧存在限制。
<br>之后还有一些后处理工作，并不是写完就完了。比如色差处理，看似简单，但实际上很考验艺术眼光。

## Effects
![image](https://user-images.githubusercontent.com/74708198/200623194-a719b6e5-2a67-4536-b6c7-2f6ee1ceaa74.png)

<br>Work with VFX: visual effects artists.
* Destruction
* Rivers
* Surface damage
* Procedural enviro animation

<br>特效艺术家通常业务精通，但并非了解手中的工具，比如他们在Houdini中做了烟雾的模拟，这部分是他们擅长的。但需要导入到引擎里以备使用，由于最终效果往往取决于贴图或者通过其他方式储存。假设，特效师创建了3个变量来控制表面破坏效果，然后TA就需要混合这些参数来做一个材质，还要加上其他因素，其他遮罩，这就属于另一种着色器的开发工作了，当你使用模拟的输入后，将这些在引擎中实现。
<br>在举例一个河流模拟的例子。 流体模拟需要上百万的粒子。不过它们可以大量删减或至少部分地烘培到贴图里去，然后你可以做一个使用flowmap的Shader，直接使用去艺术家提供的材质去混合深水浅水，泡沫等，从而做出流水击石的泡沫效果。至于程序化的环境动画，有些动画可以作假，靠模拟即可。比如衣服被微风撩动的动画。

## Workflow Research & Development
![image](https://user-images.githubusercontent.com/74708198/200838128-d25016b2-4aa6-4cb4-92e9-66e6985af78b.png)

<br>Work with Character artists, Enviro artists, Level artists.
* Sample assets
* Art style
* Testing new software
* Ways to create content for custom solutions (special materials, volumetric effects)
* Speeding up workflow

<br>在项目刚开始时，技术美术作用非常大，当然这也取决于团队的工作方式。有时小团队更倾向于采用新的技术，技术美术可以帮忙测试新的软件，找到质量高的工具，就算不确定在这个游戏项目中适不适合，至少也测试了艺术风格。比如要采用那种新的着色器或者新的光照方案，怎么样更好的导出网格，来达到某种艺术风格。
<br>接着需要创建一个样例资产，因为手里有准备好的资产，别人就可以随时打开与你讨论，“嗯不错，就要这种效果，可以在游戏中展示”。这里直接跟你的工作流有关，因为你是一个懂艺术的人，也许也会应该去决定怎样的工作流更加合适。比如有的引擎的流程非常复杂，也许我们就需要一些现成的工具去简化它，比如自动生成法线。
<br>新的游戏项目往往是简化流程的契机，去取其精华去其糟粕，从而找到新的解决方案。这里去做些改进啊，那里去开发个新的工具什么的，完善下管线或者工作流程等等。

## Optimization debugging
![image](https://user-images.githubusercontent.com/74708198/200911246-4b82230c-e8c3-41b8-ab03-e14f9635a9f0.png)
<br> Work with QA:quality assurance.
* Profiliing levels
* Finding ways to increase graphics performance
* Communicating it to artist
* Finding solutions when content exceeds budget

<br>在此阶段你需要和QA部分进行大量交流，因为测试人员会反馈很多bug，而你则需要去分析它们，你知道艺术家们哪里做错了，同时也了解引擎出了哪些毛病。比如一些常见的问题，如Unity中的Z-fighting，你会知道或许是用错了法线，或者用错了光照，也许一个地方打光过多，一个像素受到了四个光源的光照，结果就消失了，这些问题你会非常熟悉，因为你同时了解艺术和技术的内容，或者知道尝试去理解它们。当然你解决的问题越多，和艺术家，程序员交流的也就越多，你也将学到越多。所以想要进步，就应该不断去调试，改进。当你自己解决问题之后，和艺术家们交流，尝试将有效经验整理为技术文档，列出这些常见的bug以及如何规避它们。
<br>至于优化方面，你要么要向艺术家提供出更好的解决方案，让他们的作品更好地去适应程序。要么用profiler（性能分析机）进行分析，这些常见的图形调试软件，不仅可以测量性能，还可以从游戏中抓帧，看看加载了什么贴图什么网格，非常有用。

## Education
![image](https://user-images.githubusercontent.com/74708198/200909640-f66fe1c6-5287-4321-86dc-7753136b22ab.png)
<br>Work with learning and development specialists.
* Talking with engineers, then passing the know-ledge to artisits.
* Teaching new tools
* Documenting in-house tools
* Helpin with engine-specific problems
* Showing best practices and what to avoid

<br>TA实际工作中需要经常跟程序打交道，然后回头去教艺术家，就算这不是你的活，也很值得去做，因为你要去实操美术工具，所以可以用艺术家熟悉的语言去解释问题。当然有个艰巨的人物，一般没人想碰，那就是写技术文档。使用引擎，熟悉流程，然后文辞流畅地书写下来，这是个不简单的活儿，因为你没法一步到位，工具是不断迭代的，文档的也将不断更新。所以这也会是连接艺术家和程序员间的桥梁之一。

## Connecting artists with programmers
![image](https://user-images.githubusercontent.com/74708198/200928010-980b4501-0fa7-41f7-8449-b2f545c51b55.png)
<br>Work with producers
* Being a negotiator between two warring tribes

<br>衔接美术和程序是一个苦力活儿，就像夹在“联盟”和你“部落”之间谈判的中立势力，如果你的团队中美术和程序关系紧张，你就得努力弥合因为两个阵营因互相缺乏了解而产生的各种分歧。因为艺术家警察觉得莫名奇妙，为啥程序执意要求把资源存为奇怪的格式，为啥非要用一些自研的工具，而不是选择现成的，而这就牵扯到了管线问题。

## Pipline
![image](https://user-images.githubusercontent.com/74708198/200929512-c03ccd77-9882-43d7-9a43-205e166bf954.png)

<br>Work with Pipline tool programmers
* Batch tools (mass texture export, collision setup)
* Automation (LOD generation, mesh clean-up)
* Vaildation (polycount, texture count PBR correctness)
* Export between tools

<br>
