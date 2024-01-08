---
title: "2023年 年终总结"
seoTitle: "2023年 年终总结"
datePublished: Mon Jan 08 2024 07:01:54 GMT+0000 (Coordinated Universal Time)
cuid: clr4krmdq000f0ajq3qz6h7qm
slug: 2023
tags: life

---

｜ 今年的年终总结将会按照技术、生活和未来展望的大纲形式进行概述。

## 技术

先是抽时间更新了一下技术树，尽自己了解罗列了一下知识点，标明了已知、未知和熟练使用的部分。

<iframe width="768" height="432" src="https://miro.com/app/live-embed/uXjVNVXyvHs=/?moveToViewport=-1332,190,1681,879&embedId=163862384570"></iframe>

今年玩了很多很多很多新东西，一是为了尝鲜，二是为了肯定或否定一个技术

* React 18 SSR & Streaming API & React Server Component
    
* Vite Plugin & Rollup插件系统
    
* Vite配合Koa做SSR & SSG
    
* [colinhacks/zod](https://github.com/colinhacks/zod)
    
* Headless Components & radix/ui & shadcn/ui
    
* Playwright & Vitest for e2e testing
    
* Serverless/Edge Functions
    
* Rust
    
* esm module with import maps & swc/register & esm.sh
    
* 工程化相关
    

### 博客

今年总共输出技术博客5篇：

1. [OpenProps & UnoCss尝鲜](https://blog.hophop.work/openprops-unocss)
    
2. [Rust学习记录](https://blog.hophop.work/rust)
    
3. [使用changesets进行npm包发布](https://blog.hophop.work/changesetsnpm)
    
4. [现代响应式设计](https://blog.hophop.work/546w5luj5zon5bqu5byp6k66k6h)
    
5. [Wsl2 中使用网络代理配置](https://blog.hophop.work/wsl2)
    

今年在Godaddy上买了新的域名（hophop.work）。算是很久之前就想做的事情，配合Vercel做工程部署。

博客从最早的[https://github.com/citrus327/issue-blog-record](https://github.com/citrus327/issue-blog-record)（使用issue）做博客记录，到使用GitHub API提取内容渲染到静态内容，部署到gitpage上，再到迁移至Notion，使用Notionic做SSR-SSG渲染，部署至自定义域名([https://archive.hophop.work/](https://archive.hophop.work/))，最后到了迁移至hashnode([https://blog.hophop.work/](https://blog.hophop.work/))。

算是整体折腾了3次，因为今年一直在好好生活和学习Rust，导致在notion版本的博客上想要更新的内容（评论，Newsletter订阅）一直在滞后，最后因为时间因素直接选择了三方的博客平台Hashnode。

关于Hashnode：Hashnode至今已经迁移了半个月左右，已经想回归使用Notion做博客了。主要原因是Hashnode的文章编写和内容记录必须得在PC上进行。有时候在外面想记一些东西没有办法及时同步，还是得在notion上写，再迁移到hashnode。我自己的博客大多是为了作为自己的知识储备进行记录查看，现在文档跑到2个平台上且存在不同步问题确实有些麻烦。

### 开源

今年涉及到的开源项目有（PR & Discussions）：

1. [SukkaW/rollup-plugin-swc](https://github.com/SukkaW/rollup-plugin-swc/pull/45)
    
2. [mdx-js/mdx](https://github.com/mdx-js/mdx)
    
3. [web-infra-dev/rspress](https://github.com/web-infra-dev/rspress)
    

自己的开源项目有：

1. [BlizzBolts/docit](https://github.com/BlizzBolts/docit)
    
    * 见下文
        
2. [BlizzBolts/typus](https://github.com/BlizzBolts/typus)
    
    * 今年主要是郑老师在继续填坑。。
        
3. [citrus327/config](https://github.com/citrus327/config)
    
    * 主要用来记录一些配置文件
        
4. [citrus327/react-lib-starter](https://github.com/citrus327/react-lib-starter)
    
    * 基于Rollup的React NPM包脚手架，可配合scaffold或者github上直接使用
        
5. [citrus327/give-rust-one-more-chance](https://github.com/citrus327/give-rust-one-more-chance)
    
    * Rust的学习记录
        
6. [citrus327/minigrep](https://github.com/citrus327/minigrep)
    
    * 跟着Rust官网做的minigrep（加了一些功能，用了clap + termion)
        
7. [citrus327/a-rendering-limit-test](https://github.com/citrus327/a-rendering-limit-test)
    
    * 主要是验证一下React@18和React@17的一些不同，以及state变更带来的影响
        
8. [citrus327/hecto](https://github.com/citrus327/hecto)
    
    * 主要是跟着[https://www.flenker.blog/hecto/](https://www.flenker.blog/hecto/)做了一个类vim编辑器
        
9. [citrus327/an-incompatible-package](https://github.com/citrus327/an-incompatible-package)
    
    * 主要是在低端机型上做一些兼容性测试
        
10. 还有各种各样的test仓库，就不一一列出了。
    

### Docit

Docit的第三年，在没有任何推销的情况下，莫名其妙收获了22个star。今年在年中开始对Docit做了整体的[核心重写](https://github.com/BlizzBolts/docit/tree/feat/core-rewrite)。commits共176个，重写仍在继续中，跟过往不同的是：

1. 采用monorepo架构，整体分为cli, core, plugin-docit, shared, theme-default5个包。从重写初期就希望docit可以作为vite的一个独立插件运行
    
2. 部分UI上的功能迁移至Theme包
    
3. 全面更新所有dependencies。迎接更完善的MDX@2.x和Vite@4.x。
    
4. 测试驱动开发（TDD)。在整体核心处理上，做到了tc100%全部覆盖，代码80%+ coverage覆盖，在ui侧使用playwright搭配vitest做了e2e测试
    
5. 跟hash路由说拜拜，从原来的CSR产物改为纯静态的SSG产物。
    
6. 整体设计优化（感谢朋友Cid 小红书@[CiD插画设计工作室](https://www.xiaohongshu.com/user/profile/5fc8fed000000000010032e2?xhsshare=CopyLink&appuid=5b6462c9f7e8b91af7517327&apptime=1703579704)）
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703579223949/ed470d6a-ac44-4de0-a617-1571ff61c09f.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1703579168161/0ddc3448-81ad-4944-9480-c5f707f14af0.png align="center")
    

---

11月写了Docit相关的后续计划，可参考🔗[Plans for the future #38](https://github.com/BlizzBolts/docit/discussions/38)

大概国庆前后Docit停止了前进的脚步，主要是希望部分核心功能可以通过 Rust 来编写，为了性能的同时也是当Rust练习题做😈。

### Rust

🔗 学习记录：[https://blog.hophop.work/rust](https://blog.hophop.work/rust)

其实Rust的学习计划从去年就开始了，但是因为各种原因学了忘，忘了学，重新学习的计划一直拖到了今年上半年。今年在学习Rust上跟去年不同，目的性更强了，不单单是学习语言本身。

学习Rust的初衷是觉得自己需要一个脚本语言，可以编写跨平台不依赖环境的binary应用。在享受Rust高性能的同时，看到了[git-cliff](https://github.com/orhun/git-cliff)这种通过Rust编写，同时可以发布成npm包的实际用例。2021往后，Rust在前端领域生态的大规模爆发（swc, rolldown, rspack, biome等等），也让我对Rust充满信心。

在工作之外，也是花了大量的心思去学习和理解。跟着教程写了[citrus327/hecto](https://github.com/citrus327/hecto)和[citrus327/minigrep](https://github.com/citrus327/minigrep)，还有[citrus327/rust\_todo](https://github.com/citrus327/rust_todo)

先挖个坑🕳️：明年准备使用Rust重写[popomore/projj](https://github.com/popomore/projj)和[BlizzBolts/typus](https://github.com/BlizzBolts/typus)

目前因为时间原因，Rust的学习暂停在了Concurrency上，准备找个时间慢慢学完，再试试[Tokio](https://tokio.rs/)

### 工程化

#### Monorepo

从之前的Lerna + Yarn的组合切换到了pnpm workspace。迁移了一些老的项目，新的项目也完全用pnpm了。当下，pnpm workspace已经完全解决了硬盘占用 + 命令统一 + npm link的三者问题，算是2023年的monorepo最优解了。配合changesets的多包发布，整体流程非常流畅。

#### Bundler & Compiler

今年因为Docit的原因，深入去了解了Rollup的插件体系，跟Webpack相比，一样有相同的插件编排困难问题。借助一些debug工具算是可以勉强知道执行顺序以及执行结果，但整体还是略微困难，这方面也没有什么太好的解法。可能pipeline + tap的类似体系能解决debug的问题？

目前除了自己会基于Rollup弄一套自己的配置外，最常用的还是tsup，比较简单易懂。但是在比较复杂化的项目中，还是得自己自建，特别是涉及到external + 多入口不同配置的情况下，使用Rollup配置还是相对简单一点。

在bundle速度上，之前碰到一个3个入口，打包3种格式的场景，由于bundler是不能做到HMR的，所以每次watch后重新bundle几乎需要20秒样子，借助[robertknight/rollup-cache](https://github.com/robertknight/rollup-cache)，优化到了7秒样子。实际原理就是将resolveId，load，transform三个阶段的数据进行静态化缓存，每次update进行对比缓存更新。

至于compiler，在现如今卷性能但是生态跟不上的情况下，基本属于swc第一优先，其他工具爱怎么卷怎么卷了。看着生态 + benchmark选择就好。

### 技术总结

2023年是AI横行的一年。但是自己总是后知后觉，在2022年底就已经相对较早的拿到了chatgpt的测试资格，当时就想着做一些AI相关的二次套壳，但是还是没克服对套壳应用的鄙夷，选择了错过，目前感觉错过了一个亿hmmm.

关于AI, 也只停留在了使用层面，bearly, bing, chatGPT等。目前的问答类产品上，AI给我的感觉是万分的不可靠，基本回答的不在点子上，且错误率极高。解决简单的问题还是可以用的，但是当碰到一个问题，在搜索引擎上解决不了的，AI大概率也是解决不了。v0.dev也是很早的拿到了测试资格，只能说也是一塌糊涂。。等后续updates吧。

说回老本行，依旧比较确定的是要去完成Docit和学习Rust。这两点算是今年比较重比例的去关心的。从技能树角度来说，广度一定程度上已经够用了，但是深度不够。今年对于很多原理层面的东西不太关心了，什么XXX技术的核心原理，基本可以从表面大概看出内部的工作原理，不想验证也不想关心，整体给我感觉比较无聊。一定程度也说明在默默的转移到了产品维度上，我觉得算是好事儿。

## 生活

感谢父母，老蒋，卷卷，认识的n多有意思的小伙伴，当然还有Mika酱

今年算是有记录的这么些年里，最开心最完美的一年了。基本按照去年年底的计划在执行，没有给自己很大的压力去做很多事情，学了很多技术，喝了很多好酒，参加了很多活动（精酿啤酒节，音乐节，看了脱口秀），认识了新的朋友，出去玩了很多次，也在感情上补完了最后一块拼图 ❤️

### 健身

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704695205714/0a71dfbf-0ac6-4974-b213-e178ee8cdef1.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704695240749/381bada3-cfcc-4f1b-984d-83f3d6d245f7.png align="center")

从4月开始，到年底，体重从102kg减到了85kg。

最开始的目标是80kg，算是没有如约完成。但是逐渐从纯减肥的目标变成了要养成健身习惯的目标，其实一定程度上是很大的进步了。

从一开始的盲目空腹有氧 + 16/8断食 + 无碳水，到三分化力量 + 2天有氧 + 16/8断食，再到只有氧 + 16/8断食，走了很多的弯路。

其实减肥这个事情真是要把预期降低。从最开始的盲目逼迫到开始找到自己舒适的规律，其实是要花很长时间的。网上的方法有很多，找到最适合自己的才最重要。

我目前觉得最舒服的减肥方式就是三分化 + 有氧，三餐一定要保证，不一定要16/8（很容易坚持不下来）纯有氧更要补充蛋白质，不然肌肉掉的厉害（原来30kg卧推做组现在只能10kg哑铃）

减肥全程需要保证自己心情愉悦，该吃吃该喝喝，任何外在压力都是不能有的。减肥是为了享受更多更好的美食，已经很卷了，就不要卷自己了T.T

今年还学会了游泳，整体来看对游泳成绩还是比较满意的，从200斤一直游到了165斤，从最开始连20米都费劲的自由泳，到可以100，200，1000，1500。现在已经可以一口气游1000米了。100米基本在2分样子，200米约4分10秒，目前按计划一周2-3游，400米 \* 3五分钟包干也能完成。本来想2024年去考一个二级的，现在想想真是想多了哈哈哈哈哈。

### 计划

今年流行搞什么MBTI测试，跟风测了一下，结果是ENFJ，其实一定程度满足我对我自己的了解吧。

其实往年都有一直记录的习惯，今年更夸张了。

* 记吃的：[https://blog.hophop.work/happyhappy](https://blog.hophop.work/happyhappy)
    
* 记文章，关键字，书，电影等：[https://blog.hophop.work/records](https://blog.hophop.work/records)
    
* 开始记todo
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704695709545/296dc64c-1e30-45ff-971a-49534d1bf5aa.png align="center")
    

* 记日常：
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704695741802/597c604f-2dbe-4d30-ba3f-ced1e1b6e6bb.png align="center")
    
* 做详细的旅行规划
    
* 记账。
    

### 精酿

没啥好说的，本来想放图，对今年的精酿好好总结一番，但是对精酿的兴趣已经走下坡路了。

### 阅读

读了《金阁寺》《千只鹤》《许三观卖血记》《胚胎奇谭》《丰饶之海》《沉默的大多数》

还有一些读了一半的《檀香刑》《呐喊》《明朝那些事儿》《巷说百物语》

hmmm反正风格比较多，但是确定的是我比较喜欢那种将人性中的小恶无限放大的扭曲的日本文学。有些无病呻吟但啥也解决不了的文字真是读不了一点。。

其实下半年已经不咋读书了，主要还是因为酒太好喝，吉他太好弹，恋爱太香了。。

## 未来展望

生活上：

* 多体谅体谅自己，遇事先找别人原因（doge
    
* 继续健身，三分化 + 游泳。等jio好了开始跑步。变成肌包脂。体重维持在165左右。体脂到18。
    
* 少喝点酒吧，中药继续吃。
    
* 工作上的事情还是少操点心。
    
* 好好谈恋爱，别整幺蛾子。
    
* 吉他继续弹。
    
* 自由泳学习4次和6次腿，100米进1分40。可以一口气1000米自轻松拉长游。
    

技术上：

* 把Rust学完，持续学习。
    
* 算法每天一题。
    
* 找到自己最擅长的技术点。
    
* 持续写文章，尝试翻译文章发布。