---
title: "2019 年年终总结"
datePublished: Mon Dec 30 2019 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqaji18v000308jmebqk3uld
slug: 2019
tags: life

---

## 引子

从来没有想过写年终总结，也没有对自己的技术做过整理和归纳。2019 年是正式做前端的第一年，也是从生活上和技术上变化最大的一年。借此机会梳理归纳当前的知识树和将要学习的内容。

## 2019 年

### 计划小结

在 2018 年的年底，有定制过 2019 年的学习计划。

> Learn TypeScriptLearn React and build a project with ReactLearn Git and GithubContribute to the Open Source communityLearn CSS animationClear easy difficulty in LeetCodeLearn Express.jsBuild a Vue.js functional component libraryWrite a blog about what I learntAt least achieve three goals above

以上的每个计划其实都有接触和实现，有些是实现了，有些是中途废弃了。

2018 年底是我刚刚接触并进入前端领域的时候，可以看出那时候的学习思路还是在掌握一些基本的语言层面，学习一些工具和基本框架，尝鲜的部分比较多。

那时候有了解 NativeScript，有了解 Node.js，认为 JavaScript 要大一统，“唾弃”别的语言，认为 JavaScript 是未来。

其实现在也觉得 JavaScript 是未来，但是思考的角度不同。技术是用来解决问题的，现在会去思考”为什么会有 NativeScript, react-native“，”为什么会有 Node.js“，“他们解决了什么问题”

例如想要学习 TypeScript，只是因为 TypeScript 被人追捧的多，而没有实际去考虑和理解 TypeScript 带来的好处。在初期写代码的时候，一味的使用 TypeScript，但是对其复杂的语法、泛型、类型推断等没有足够的了解，网上的很多最佳实践并没有解决我碰到的问题。

例子还有很多，说白了就是没有清楚自己需要解决的问题在哪里，那解决方案也就是空谈了。

### 工作小结

2019 年正式成为了一名前端工程师。主要技术栈就是 JavaScript 和 Vue.js。

在工作中碰到了很多沟通和实际解决方案的问题，在初期其实踩了很多的坑，也犯了很多的错误。

例如问题没有及时沟通导致 task 进度延缓耽误了 release；代码过度设计导致维护困难；缺少沟通导致很多无意义的加班熬夜。

## 技术积累

### Vue.js

2019 年在 Vue.js 上其实提升并不大，其实 Vue.js 能够玩出的花样并不是很多，痛点无非在于组件的抽象封装，路由的分解，Vuex store 的模块拆分。 这里简单概述一下自己的想法。

Vue.js 对于一般的组件来说保证 props 向下，event 向上基本就没什么问题。

组件的设计讲究受控与非受控的共同设计，一个优秀的通用组件必须提供受控模式和非受控模式 2 种才能够达到基本满足需求变更的需求。当然在业务场景中要充分考虑实际应用场景，因为一个复杂组件同时支持受控与非受控，是很复杂的，会有很多的冗余的 local state 和 event 抛出。

Vue.js 对于单个不可拆分的职责很难拆分，如果有一个 BaseComponent，想对其进行拓展，只有使用 mixin 或者是加入更多的 prop。这两种方式对于组件的后期维护和复用来说都是噩梦。

mixin 带来很多命名空间问题， 数据和方法来源通常无法追溯。这种基于继承而非组合的方式，在多个 mixin 介入进来之后，会发生很多意想不到问题。mixin 中对于状态合并，生命周期调用顺序，是有一定的规律的，在多人开发或者换人维护的情况下很容易造成困扰进而引发 bug。mixin 对于一个组件来说，只有拓展功能，如果我想要一个 mixin 中的一部分内容，而不要另一部分，除了分解当前 mixin 为多个小 mixin 单独引入之外别无他法，这种只能做加法不能做减法的方式对于长期维护和多处调用的组件来说只有弊没有利。

还有一种就是加入更多的 prop，这个对于一般人来说很容易就能看出来这种方式的缺点在哪里。这里可以借用一个例子，Prettier 的官网有篇文章叫[Option Philosophy](https://prettier.io/docs/en/option-philosophy.html)，这文章大概就是说为何 prettier 不提供很多 option 供调用方进行自定义。当然这个文章主要说的是如果提供的自定义项过多，只会造成开源项目的难易维护进而影响项目的生命力。我觉得这个可以应用到 Props 的这个问题上来，选项过多代表着功能过多，功能过多就会很多的边缘用例，处理各种各样的边界问题会导致组件代码冗余复杂进而引发 bug。

这里提到了 2 种官方里提到的比较普遍的组件拓展方式，其实还有 slot，scoped-slots, hoc, 利用 scoped-slots 构建 renderless-components 等方式来构建复杂的抽象的组件。其实这里说的有点偏题了，以后可能会单独开一篇文章细说。

### React.js

工作中是完全没有 React.js 的应用场景的，纯靠自己推敲、实践和学习。 现在可以通过 React.js 完成一个基本的项目。 很多语法还是不熟，hooks 方面除了 useState, useEffect 以外的 hooks 基本没用过。 因为有 Vue.js 基础，像 hoc, render props 基本可以类推。 状态管理方面采用原始 Redux 配合 Redux-thunk 做状态副作用。

### CSS 预处理器

在我目前的工作场景中，其实就是指 Sass。 Sass 对于我来说，其实只是做一个嵌套语法，sass 的变量只能说是一个辅助，并没有解决什么实际问题。 sass 的变量无法通过 js 操作，毕竟 sass 是个预处理器，很多事情都是在 compile time 完成的。目前产品线上要求有自定义的 runtime branding 要求（换肤），其实 sass 是没法做到的（可能会有解决方案吧，但是官方是没有提供 runtime 级别的 api 用来操作 sass variables 的)

私下里有去了解别的 css 预处理器，看上去基本差不多，都是自己的语法，基本就是嵌套语法，语法简化，变量，mixin，函数之类的，主要是想做到代码重用。

在目前产品线上没有让我去单独负责一些 css framework 的工作，其实没有运用到 mixin，函数之类的高级特性，最主要的就是嵌套语法。 如果日后有碰到实际问题，再重新捡回来看吧。

在年末的时候仔细看了下 PostCSS，PostCSS 官方阐述说他并不是一个 css 预处理器。而我的理解是，PostCss 有点像是 css 里的 webpack，拥有很多的插件可以基于原始 css 进行拓展。例如我常用的嵌套语法就可以使用 postcss-nested 插件，css variables 可以使用 postcss-variables，还有很多的插件基于此进行拓展。也就是说 postcss 其实是可以借助其生态来达到 less, sass, stylus 等预处理器的效果的。只不过一开始的目的不同，postcss 基于插件用来拓展自己的功能，而其他的 css 预处理器则提供一整套解决方案。PostCSS 的 autoprefixer 插件非常知名，很多人都是通过 autoprefixer 来了解到 postcss 的，而不清楚他本身的目的。

### 包管理

其实包管理这部分的技术积累单纯只是因为之前完全没有包管理的概念，也没有这方面的场景。 主要就是利用 Yarn 的 workspace，配合 Lerna 做 Monorepo。目前公司项目使用的就是 Monorepo，但是又没有充分利用他的特点做相互引用，只是拿来把 dependecies 放到最外层。。

自己私下里的项目其实有尝试去做，除了踩了无尽的坑和无尽的坑之外，等着我只有更多的坑，主要还是各种工具类的配置共享，配置继承，单独为某个 package 添加依赖，升级依赖等问题。不过毕竟是自己折腾的项目，整体复杂度摆在这里，暂时没有围绕着做相关生态和大力开源的想法。monorepo 这个事情可行，但是没必要。暂缓。

### 工程化

产品早期的工程化就是基于 webpack 搭的架子，无非就是各种 plugin 和各种 Loader，公司也不存在多部门共享一个脚手架所谓的场景。所以其中经验都是借鉴公司脚本和自己摸索出来的。

目前工程化还是基于 Vue.js 的经验比较多。Vue.js 主要是基于 Vue-Cli 进行项目搭建的，Vue-Cli 提供了很多的 built-in 特性，例如开启 eslint，选择代码风格，选择是否使用 typescript, 是否使用某个测试插件等等等。Vue-Cli 算是比较通用的脚手架工具了，提供的配置项也很多，webpack 的配置也可以通过 webpack-chain 来进行拓展。

因为目前工作中没有多部门使用同一个脚手架，或者说封装脚手架来共用的场景。在私下里开发的项目中，基本项目开发使用这种通用脚手架是很不错的选择，毕竟官方出品，坑也少。

因为目前所在的公司所面对的场景不足，没有实际的共用脚手架的需求。所以我只能理想化的猜想一个跨部门的前端脚手架肯定是基于一套统一协商好的前端文件架构，统一的编码风格，当然对于其中不统一的部分，提供多个模板，而不提供过多的自定义。因为这个脚手架毕竟是面向内部，约定大约规范。

工程化中除了利用打包工具解析各种文件格式，载入插件，提供开发环境 dev server 等功能，打包性能优化也是其中很重要的一点。 打包性能优化无非

1. 压缩代码
    
2. 减少首屏渲染载入的文件大小
    
3. 通过 hash/contenthash 进行缓存级别的优化
    
4. 通过 webpack dll 记住之前打过包的部分，减少打包时间
    
5. 通过 splitChunk 将原有一个 app.js 分成一个或多个 vendors，减少文件大小。
    

目前的工作场景也只能够让我摸到这几个部分，很多细节其实是需要更大的场景去设计与实践的，凭空想象业务场景，只会额外花时间解决伪需求。

### 前端性能优化

这部分主要说说代码和个别 feature 的实现上的优化。 目前碰到的可以被优化的点有

1. 使用[Functional Components](https://vuejs.org/v2/guide/render-function.html#Functional-Components)解决多个无状态 vue 组件渲染问题。
    
2. 使用虚列表[vue-virtual-scroller](https://github.com/Akryum/vue-virtual-scroller)解决大列表渲染问题。不过这个坑蛮多的，很多时候列表改变，但是 virtual-scroller 重新渲染导致 dom 没有及时变更，展示空的占位符之类的问题。
    
3. 使用图片懒加载减少图片请求个数。
    
    * 图片懒加载主要的机制就是如果图片在视窗内，就载入图片，否则不载。这个问题被分成 2 个部分，一个是如何判断在视窗，第二就是如何延迟发出请求家在图片。
        
    * 第一个问题其实有很多解决方案
        
        * 使用 dom 距离 viewport 上下左右的距离判断是否在视窗内，这个可以用 offsetTop 之类的属性可以做到，或者使用[Element.getBoundingClientRect()](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect)获取距离然后判断。但是任何只要实时获取距离的，宽高的，就会触发 reflow。这个如果影响的地方很多的话，会对性能有很大影响，所以就有了方案二。
            
        * 方案二是利用较新的 api: [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)。这个新的 api 会动态监测 dom 的位置，而不会触发 reflow。需要借助[polyfill](https://github.com/w3c/IntersectionObserver#readme)
            
    * 第二个问题就是延迟载入图片，图片的载入是在 img 的 src 被实际赋予值的时候发出请求的。所以延迟载入可以先创建 img 标签，当 img 标签在视窗范围内时，将 src 由空置成实际值。
        
    * 这 2 个问题有一个库解决了，那就是[Lozad](https://apoorv.pro/lozad.js/)
        

## 技能树

以下的技能树是我目前能够想到的技术点。我会根据这颗树进行 2020 的规划编排。

![https://user-images.githubusercontent.com/17166940/94899504-0b075e00-04c6-11eb-8132-4d8bd72d4373.png](https://prettier.io/docs/en/option-philosophy.html)

## 总结

2019 年是学习的东西最多的一年，但是很明显可以看出来东西浮于表面，没有更深层次的深入。 很多待解决的问题需要被列出来留到 2020 年解决了。

针对 2019 年的计划，留下了几个疑难点

1. 各大开源项目的贡献方式各不同，如何正确的提交 pr？如何反馈？
    
2. TypeScript
    
    * TypeScript 的类型文件如何管理？
        
    * Tslint 淘汰后，如何配置 Eslint？Monorepo 的情况下呢？
        
    * 如何给 js based 的 lib 加上 typescript 类型支持？
        
    * 多个 TypeScript 项目构建 Monorepo，如何配置配置文件？
        
3. React.js
    
    * React.js 学习的时间不够集中，很散，没有彻底经历过 React 的版本迭代，是很难看出来他每个版本都解决什么问题的。所以需要重新浏览一遍 React.js 的文档，生命周期函数。 好像有取消一些老的生命周期钩子，了解其原因和替代品。
        
    * React.js 的 fiber 算法算是初步了解，但是 fiber 是如何计算任务耗时，是如何编排任务的呢？
        
    * React.js 可以通过一种 React.memo 进行状态浅对比，如何配合 immutable.js 作性能优化？最佳实践？性能提升多少？
        
    * React.js hooks，如何最大化利用 hooks 进行状态和副作用的封装？最佳实践？可以做什么，不可以做什么？
        
4. 工程化。目前其实除了 monorepo 之外，工程化问题并不是很多，有些最佳实践不是自己捣鼓就能捣鼓出来的，需要更多的场景和理解才能有答案。所以可能转变思路，看一下通用的项目脚手架是如何搭建的？可以通过阅读 Vue-Cli 的源码来提升。
    
    * 了解 Vue-Cli 设计理念
        
    * Vue-Cli 做了哪些事情？想要做到什么地步？
        
    * 个性化到什么样的程度？
        
    * 如果通过插件机制对脚手架进行拓展？
        
5. 移动端。这个反正是一片空白，也不准备自己研究。有实际业务场景就会解决。
    
    * H5?
        
    * React Native?
        
    * Flutter？
        
    * 小程序？
        
    * 公众号？
        
6. Node.js。基本不准备在深究了，因为目前场景很少，能够基于此搭建基本 Rest 服务就满足当前需求了。
    

## 2020 年计划

1. 效仿 Antd, 使用 rollup 构建 monorepo，尝试搭建自己的组件库或组件库脚手架。
    
2. 学习服务端渲染技术，使用 expressjs 实现最基本的 ssr 服务
    
3. 学习 next.js, 并完成 shynode-blog 端
    
4. 使用 egg.js 完成 shynode-eggjs 的 nodejs 后端
    
5. 使用 React.js 完成 shynode-admin 的前端
    
6. 深入学习算法，[I-Algorithms](https://ziyi2.github.io/algorithms/guide/introduction.html#%E5%AD%A6%E4%B9%A0%E6%96%87%E6%A1%A3) 刷 leetcode
    
7. 使用 React.js 高级特性，使用 Immutable.js 和 React.memo 实现性能优化
    
8. 尝试复刻网易云音乐，做 PWA 或者 H5 应用，参考掘金小册
    

## 2020 年侧重点

1. Monorepo 工程化搭建
    
2. 深入理解和应用 React.js
    
3. github 贡献，pr 提交步骤