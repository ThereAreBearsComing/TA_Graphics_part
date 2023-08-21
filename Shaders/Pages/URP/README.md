# [Universal Render Pipeline](https://blog.csdn.net/mango9126/article/details/126418176?ops_request_misc=&request_id=65a078a2403b4e43bb00c3ab9bd540d7&biz_id=&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~koosearch~default-3-126418176-null-null.268^v1^control&utm_term=urp&spm=1018.2226.3001.4450)
最核心的优势是相比内置管线提高渲染效率，附带提高渲染效果。手机上，渲染效果的限制主要是性能方面，而不是技术方面

另一个优势是方便定制管线，可以单独更新package，在不更新unity版本的情况下单独更新URP代码。我们项目的做法就是用本地的package，有新的功能把代码合并过来，相比更新unity要容易的多

对于开发来说，了解源码，面对一些bug和需求，更容易发现问题，以及选择更好的解决方案

## [管线驱动](https://blog.csdn.net/mango9126/article/details/126418188?spm=1001.2014.3001.5502)
管线做了两件事，一是设置各种全局数据，二是驱动各个pass去做真正的渲染

## [默认渲染实现（核心部分）](https://blog.csdn.net/mango9126/article/details/126418218?spm=1001.2014.3001.5502)
URP 12之前是单pass前向渲染，到12后已经不是了，URP内置forward和deferred渲染。ForwardRenderer也改成了UniversalRenderPipeline

当然前向渲染的单pass，并不是只能调用一个pass，而是对光照而言，没有之前内置管线的forward base和add，而是在一个pass里，处理多个光源的计算。需要多个pass实现的效果，需要设置DrawingSettings的shaderPassNames，就会依次调用指定名字的pass进行渲染了
<br>![image](https://github.com/ThereAreBearsComing/aBookOFtechArt/assets/74708198/eb8a1f81-ee5d-4c55-8858-9bfb190f7d08)


## [光照处理](https://blog.csdn.net/mango9126/article/details/126418242?spm=1001.2014.3001.5502)
URP支持forward和deferred两种光照策略

forward中，光照计算在一个pass，循环每个光源数据计算，也就是一个物体的光照，在一个drawcall计算完成，而不用每个光源一个drawcall。这样的好处是可以有多个光源，但又不会造成drawcall翻倍的现象。只是目前URP的版本，对光源和阴影有一些限制，后续会详细说明

deferred实现方式和传统的有一些优化，后续更新在光照部分


## [阴影处理](https://blog.csdn.net/mango9126/article/details/126418268?spm=1001.2014.3001.5502)

## [后处理](https://blog.csdn.net/mango9126/article/details/126418290?spm=1001.2014.3001.5502)

## [获取各种rt的方式修改](https://blog.csdn.net/mango9126/article/details/126418331?spm=1001.2014.3001.5502)

## [SRP Batcher]()

## [对渲染框架的理解]()





