---
title: "OpenProps & UnoCss尝鲜"
datePublished: Wed Nov 22 2023 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqak439p000l08jt2ahu8ova
slug: openprops-unocss
tags: css

---

## 简介

[https://2023.stateofcss.com/en-US/css-frameworks/上看到OpenProps](https://2023.stateofcss.com/en-US/css-frameworks/%E4%B8%8A%E7%9C%8B%E5%88%B0OpenProps) 和 UnoCss

这是一次尝鲜使用的使用记录。

## TL;DR

OpenProps通过强大的Css Variables来构建一致风格的组件（Html片段）

UnoCss，一个性能好，DX好的Atomic Css方案

两者之间的选择：

* 快速Prototyping选择OpenProps
    
    * Pro：简单易用
        
    * Con: 请求多，css variable的维护难易程度见仁见智
        
* 实际生产使用UnoCss
    
    * Pro：Atomic Css
        
    * Con：Atomic Css
        

## OpenProps

[https://open-props.style/](https://2023.stateofcss.com/en-US/css-frameworks/%E4%B8%8A%E7%9C%8B%E5%88%B0OpenProps)

### 使用

```css
@import "<https://unpkg.com/open-props/src/index.css>";

button {
  color: var(--blue-6);
  font-size: var(--font-size-8);
  font-family: var(--font-serif);
}

button.sans {
  font-family: var(--font-sans);
}
```

打包产物如下：

```jsx
var css_248z = `
	@import \\"<https://unpkg.com/open-props/src/index.css\\>";
	button{color:var(--blue-6);font-family:var(--font-serif);font-size:var(--font-size-8)}
	button.sans{font-family:var(--font-sans)}
`;
styleInject(css_248z);
```

但是运行时会有以下请求。

![](https://file.notion.so/f/f/11f66c5f-6043-406b-b492-4dc267645668/d0659301-0a68-47b0-86a9-04b72468cbd3/Untitled.png?id=abf7f0c3-af3a-4171-bd6a-f8b6316fc782&table=block&spaceId=11f66c5f-6043-406b-b492-4dc267645668&expirationTimestamp=1702972800000&signature=bMWL5vIG44hVF2z17CkCOat5ETyYbJbuQ280_JSgZjE&downloadName=Untitled.png align="left")

### 产物优化

可使用`postcss-jit-props`

```css
button {
  color: var(--blue-6);
  font-size: var(--font-size-8);
  font-family: var(--font-serif);
}

button.sans {
  font-family: var(--font-sans);
}
```

使用`postcss-jit-props`可以做到按需引入

```jsx
// postcss.config.js
const postcssJitProps = require('postcss-jit-props');
const OpenProps = require('open-props');

module.exports = {
  plugins: [
    postcssJitProps(OpenProps),
  ]
}
```

产物如下：

```jsx
var css_248z = `
	:root{--blue-6:#228be6;--font-size-8:3.5rem;--font-serif:ui-serif,serif;--font-sans:system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,Cantarell,Noto Sans,sans-serif}
	button{color:var(--blue-6);font-family:var(--font-serif);font-size:var(--font-size-8)}
	button.sans{font-family:var(--font-sans)}
`;
styleInject(css_248z);
```

可以发现以上css variables被自动引入。对应没有使用到的css variables会被purge。

### 结论

1. 参考仓库：[https://github.com/citrus327/openprops-test](https://2023.stateofcss.com/en-US/css-frameworks/%E4%B8%8A%E7%9C%8B%E5%88%B0OpenProps)
    
2. 纯css variables类的css解决方案。
    
3. 配合[postcss-jit-props](https://github.com/GoogleChromeLabs/postcss-jit-props)可做到按需。
    
4. 没有什么槽点，要自定义有自定义且因为纯css variables没什么性能科研
    

## UnoCss

又又又是一个atomic css解决方案

还是那些老生常谈的优点

1. 定制化：config file配置
    
2. 按需：基于atomic-css engine的css快捷生成
    
3. @apply的原子化样式应用
    
4. 等等等等。。
    

### 结论

1. 参考仓库：[https://github.com/citrus327/unocss-test](https://2023.stateofcss.com/en-US/css-frameworks/%E4%B8%8A%E7%9C%8B%E5%88%B0OpenProps)
    
2. 整体接入流程跟tailwindcss类似，参考官网即可。但是在rollup作为bundler下，启用watch mode，会出现构建产物失败问题。build就没有问题。
    
3. 产物完全按需，性能没话说。tailwindcss、windicss平替。
    
4. 生态工具及其夸张，就怕DX不到位，甚至做了dev inspector和runtime方案
    
5. 文档上做的很好，atomic css就怕记不住一直要翻文档，unocss官网采用了搜索的方式 [https://unocss.dev/interactive/?s=font-serif](https://2023.stateofcss.com/en-US/css-frameworks/%E4%B8%8A%E7%9C%8B%E5%88%B0OpenProps)