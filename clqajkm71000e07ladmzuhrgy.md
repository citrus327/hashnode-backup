---
title: "2021 年年终总结"
datePublished: Thu Dec 30 2021 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajkm71000e07ladmzuhrgy
slug: 2021
tags: life

---

分为技术与生活两部分，写给自己看，后面喷自己用

## 技术

先总结一下，再细说。

* Typescript 已经算是比较熟练的层面了。
    
* 深入了解与使用了 Vite。产出了文档方案 docit
    
* React hooks 的第 N 次重新理解，也重新阅读了 N 次官方文档。
    
* 参加了 Vue Conf，远观了几位社区活跃人士
    
* 整体系统架构设计能力提升
    
* 公司里帮团队做 KPI 产物
    

### TypeScript

今年基本项目都开始迁移 TypeScript 了，相比以往的 TypeScript 体验，这次还是蛮顺畅的。从复杂的 typing 定义，到重载，类型守卫，已经比较得心应手了。

对于 TypeScript 自己还是一如既往的觉得，迁移需谨慎。

面对频繁的业务迭代速度，TypeScript 对于快速”糊“一个上去的诉求明显不够灵活，且对团队水平要求更高。见证过 AnyScript 真不稀奇，DictionaryScript 才真是血压飙升

```tsx
interface Dictionary {  [key: string]: any;}
```

若在团队内推广 TypeScript，一定要确保自身对 TypeScript 的了解足够深入，能够进行一定规模的培训，对于很多难写的 case（React, Event, Forward Ref, Hoc 等）能够给予团队快速且正确的解答。

### Vite

Vite 也是今年大热门了，开发体验确实是从未体验过的爽快，虽然目前公司内还未正式使用过，但是私下里已经基于 Vite 开发过 2 个项目了。 公司内未正式使用主要还是研发平台中部分链路对于 site 应用的把控，希望明年能自己尝试突破一下。

再说一个自己在做的玩具 Docit

Docit 是一个快捷文档编写解决方案，目标在于简单快捷，能够快速构建文档。 基于 Mdx + Vite，支持基于 TypeScript 的快速类型表格生成。

后续会支持 SSR 等更多功能，目标是好用，可用，快速。对标 Storybook 甚至 VuePress

### Unified

没太多可说，从 Mdx 开始到整个 unified 生态学习。

### React

以目前的技术水平以及我的描述能力其实不是很好表达我对 React 的看法，但是我还是大概说说

先提一些疑问吧，

1. 从一个页面的渲染角度出发，父组件 render 了，我子组件为何要 rerender，从单纯的组件出发，我依赖我的入参，我依赖我自身的状态，仅这 2 点变更时我进行 rerender 足矣，为何放在父组件里就必须要遵循父组件 render，我必须 rerender 的说法？
    
2. useEffect 作为一种新的编排副作用的方式真的挺好的，eslint 针对 deps 的提示也挺好的，但是部分需求确实要强行关掉提示。当然可以用 useRef，缓存手段让 useEffect 不对缺少的 deps 报警，但是总觉得不是一个理想的方式。别的 hooks 同理。
    
3. 一个组件是否要用 React.memo 包装，这个问题其实跟问题 1 挺像的，在我看来，一个组件就必须要用 memo 包上（当然我从来不这么写），我不认为目前父子组件的 render 问题是一个好的现象。
    

我觉得其实去细扣这些细节对于我个人而言没有什么太大的意义。因为从性能角度，逻辑编排角度，状态唯一性等设计问题我觉得我已经做的还可以了，有进步空间，但是也不知去哪儿进步。我明年核心应该是关注于技术多元化，而不是对 React 扣字眼。

说到细节，Redux 是一个典型的 FLUX 架构实现，思路很美好，写起来是真的吃屎。跟 React 核心思路一样，状态是状态，不跟 render 强绑定的不是状态，不应该作为 dep 的不要作为 dep，保证逻辑唯一性，等等等等。用一些很 hack 的方式去保证这些美好的思路，但是不考虑代码可读性真的好吗？

再说说状态管理，Redux 之后紧跟着各种”Wrapper“就来了，例如 rematch, ice store 等状态管理，要么是减少 boilerplate 代码，要么是用 immerjs 做一些黑魔法。要么就是拥抱 hooks 的状态管理，可 hooks 紧锁 React 生态，有些状态管理 lib 甚至脱离 React 无法使用，这些是否真的是一个好的前端应用状态管理呢？

### DSL

其实写到这里，我已经有点费解了。对”大厂“的费解其实是更多，我就不提 Weex 到底发生啥了，去年下半年听说了 Rax 这么个玩意。 我能体会到现在国内各种内卷的状态，各类业务对于前端的高速迭代诉求，uniapp，rax，remax, weex 等应运而生。

前公司是国外一小企业，有 Android 和 Ios 的开发诉求，同时也有 web 端的诉求，在我看来一行人干一行事儿，前公司是专门安排了 3 个团队对应 3 个端的，我觉得这种是真的很好，对于工种分配的很合理。

先说说 Rax，目前我司主技术栈是 react，那你现在要搞 Rax，之前构建的 React 怎么办？社区生态不要了？那 Rax 哪有社区生态呢。 Rax 估计也就跟 Weex 差不多了。

《部分内容已删减：巨人之前也没法站在巨人肩膀上看世界》

> 行了，今年的年终总结最终还是把自己写成了个喷子模样

### 生活

略

## 总结

回顾去年：

* 明显戾气重了很多，就是那种感觉自己技术贼牛逼的傻逼气息，希望新的一年能够收敛。
    
* 技术上感觉上涨 20%？
    

期望新的一年：

* 出去旅游
    
* 住的好一点
    
* 多看看父母
    
* 要开始考虑技术多元化
    
* 为公司考虑多一些些，为自己考虑更多一点
    
* 多读书，多写文章，多锻炼锻炼文笔
    

学习列表：

* \[x\] Svelte & Web Components 及其生态
    
* \[ \] Canvas
    
* \[x\] Vite & Rollup & Esbuild
    
* \[ \] Workers & Google Workbox & PWA
    
* \[x\] Performance Tuning