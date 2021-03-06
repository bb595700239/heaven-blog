---
date: 2017-09-25 23:50:00 +0800
title: 浅谈前端中的过早优化问题
description: 过早优化是万恶之源。 —— Donald Knuth 还记得大四的软件优化课上，课的主题虽然是关于各种优化手段的，但老师讲的第一门课的 PPT 里依旧给这句话留了独立的一页。在近期的工作中，我就碰到了不少因为“过早优化”带来的麻烦。稍微整理了一下思绪，便有了这篇短文。
permalink: /posts/talk-about-premature-optimization/
key: 10040
labels: [前端开发, 性能优化]
---

> Premature optimization is the root of all evil.
> 过早优化是万恶之源。 —— [Donald Knuth](https://zh.wikipedia.org/zh-hans/%E9%AB%98%E5%BE%B7%E7%BA%B3)

还记得大四的软件优化课上，课的主题虽然是关于各种优化手段的，但老师讲的第一门课的 PPT 里依旧给这句话留了独立的一页。在近期的工作中，我就碰到了不少因为“过早优化”带来的麻烦。稍微整理了一下思绪，便有了这篇短文。

## 1. 什么是过早优化？

翻阅了一下知乎上[轮子哥对于这个问题的看法](https://www.zhihu.com/question/24282796/answer/27279410)，还是很符合我的看法的：

> 过早指的不是在开发过程的早期，而是在还没弄清楚**需求未来的变化的走向**的时候。你的优化不仅可能导致你无法很好地实现新的需求，而且你对优化的预期的猜测有可能还是错的，导致实际上你除了把代码变复杂以外什么都没得到。

在我看来，过早优化的特征有二：

- 对所要实现的系统没有一个全面的规划，是为其一；
- 需求本身不够稳定，或对需求的理解不够透彻，是为其二。

没有全面的规划，就容易拘泥于细节。对于需求分析不够清晰，则往往会制造许多南辕北辙的废代码。在时机仍不成熟时，就针对一些细节上动用太多的脑筋、早早地开始优化性能，不仅会大大地增加代码复杂度，而且这些优化在很多情况还会对后面的开发工作增添大量的障碍。

最近在开发基于 Draft 的富文本编辑器时，需要完成一个表格编辑的功能。由于 Draft 本身的扁平化结构，要在它上面构建一套表格编辑的交互是是一件非常麻烦的事。

在初期我并不知道如何去实现这个表格功能，所以写了好些预研性质的代码进行测试。其中，便使用了 `shouldComponentUpdate()` 来避免**不必要的渲染**。在不断补全各种功能的同时，表格渲染总会出现这样那样的问题。后来经过仔细检查，我发现原来我在表格组件生命周期中所实现的 `shouldComponentUpdate()`，在某些情况下把需要重新渲染的状态也给跳过了。相信很多 React 开发者也会遇到此类问题，比如来自石墨的这位兄弟便在[文章](https://zhuanlan.zhihu.com/p/29103532)中把自己批判了一番。

> 我们在迁移到 React 的初期，由于团队只有少数几个人接触过这个框架，所以经历了一段时间的「阵痛」，例如对于 shouldComponentUpdate 的错误使用使得一些错误变得不易追踪调试。……

另外，**前端的缓存策略**也是一个单页应用需要好好考虑的优化点。如果缓存基本难以命中倒还好，如果应用一直在使用已经过期的缓存，问题就很大了。一旦在项目中使用了没有经过推敲的缓存失效策略，很容易就会导致很多难以预期的结果。（所以目前我们已经在我们的应用中把缓存暂时禁用了，等应用的大部分功能都稳定以后再去考虑怎么合理地控制缓存失效的策略）

## 2. 避免过早优化的原则

今天公司的 AMA 上，严老师展示了一下他上周去深圳学习交流的体验。其中他反复说道的，也是来自腾讯的讲师们反复提到的一句话是这样说的：**先抗住，后优化**。这句话当然是很后端工程师做派的，不过我觉得对前端也适用。

展开来说，要避免过早优化，只需要注意两点：

- 逐步完善系统，不追求一步到位；
- 在考虑优化的同时，需要清楚它所带来的收益和影响范围。

如果优化并不是在写代码时需要第一考虑的点，那什么才是更需要关注的呢？我的答案是：**代码的可读性和可维护性**；展开说还包括了代码的复用性、抽象层次。别太担心性能。你要多好的性能，“比尔”都会帮你从“安迪”那儿抢过来的。[doge]

> What Andy gives, Bill takes away. —— [安迪-比尔定律](https://baike.baidu.com/item/%E5%AE%89%E8%BF%AA%E6%AF%94%E5%B0%94%E5%AE%9A%E7%90%86?fromId=2066996)

## 尾声

过早优化时常出现在我们的开发过程中，不知不觉就会为我们的 code base 带来不必要的负担。时刻感知到这一问题的存在，知道何时该优化、何时不该优化，是一名优秀的软件工程师必备的素养之一。

避免过早优化不是说就不用优化了；当在系统遇到瓶颈时，优化在所难免。而如何进行系统性能瓶颈的检测以及如何进行优化又是另一个很大的话题了，此文就不做赘述啦。
