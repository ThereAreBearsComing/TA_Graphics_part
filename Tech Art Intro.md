# TA 职责和技能介绍
TA基础

## Intro
![image](https://user-images.githubusercontent.com/74708198/200616393-fed5ee20-427b-4697-b5cc-56f06e53b836.png)
<br>要对使用引擎十分了解，eg.当使用Ureal的可破坏网格时，就知道这个工具有很多Bug，而了解如何使用这些工具并且规避Bug，就是有用的经验。

## Shaders
![image](https://user-images.githubusercontent.com/74708198/200617570-ed9175d4-04b7-4d56-9f53-a54c70fcc80a.png)

<br>Work with Texture Artists:
* Developing shaders(Code or node-based)
* Non-standard materials,
* Animated materials
* Difficult cases (e.g. glass)
* Post process

<br>开发着色器并非那么简单，你需要明白技术限制带来的性能损耗。例如，一个简单的着色器里需要放多少材质样本，特别是当没有标准材质可使用时，就会需要用到复杂材质，比如1高级冰面材质，或者熔岩，皮肤和动态材质这类非标准材质，因为现在很多动作也可以直接在着色器中实现，比如各种霓虹灯，全息投影等效果。当然还有很多非常复杂的材质，例如结霜额草皮等，还需要修改引擎，从引擎给予支持。所以我们在实现各种效果时应充分考虑各种限制因素，毕竟不论如何现在的硬件依旧存在限制。
<br>之后还有一些后处理工作，并不是写完就完了。比如色差处理，看似简单，但实际上很考验艺术眼光。

## Effects
![image](https://user-images.githubusercontent.com/74708198/200623194-a719b6e5-2a67-4536-b6c7-2f6ee1ceaa74.png)

<br>Work with VFX: visual effects artists
* Destruction
* Rivers
* Surface damage
* Procedural enviro animation

<br>特效艺术家通常业务精通，但并非了解手中的工具，比如他们在Houdini中做了烟雾的模拟，这部分是他们擅长的。但需要导入到引擎里以备使用，由于最终效果往往取决于贴图或者通过其他方式储存。假设，特效师创建了3个变量来控制表面破坏效果，然后TA就需要混合这些参数来做一个材质，还要加上其他因素，其他遮罩，这就属于另一种着色器的开发工作了，当你使用模拟的输入后，将这些在引擎中实现。
<br>在举例一个河流模拟的例子。 流体模拟需要上百万的粒子。不过它们可以大量删减或至少部分地烘培到贴图里去，然后你可以做一个使用flowmap的Shader，直接使用去艺术家提供的材质去混合深水浅水，泡沫等，从而做出流水击石的泡沫效果。至于程序化的环境动画，有些动画可以作假，靠模拟即可。比如衣服被微风撩动的动画。
