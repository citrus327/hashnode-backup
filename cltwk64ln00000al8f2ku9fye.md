---
title: "使用 rollup 构建 library 的一次尝试"
datePublished: Mon Mar 18 2024 06:22:09 GMT+0000 (Coordinated Universal Time)
cuid: cltwk64ln00000al8f2ku9fye
slug: rollup-library-1

---


# 使用 rollup 构建 library 的一次尝试


> 因为在设计博客和 CMS 的时候，有一个设想就是在 CMS 里去编辑 md，同时实时预览，这个预览和实际提交在博客上展示要求完全一致。所以就想着自己弄一个预览模块，在两个项目之前共享代码。这就涉及到一个 Markdown Preview 的工具。这个工具其实就是从 MD => HTML，使用 highlight.js 做代码高亮，一些自定义的配置和样式文件，然后打包成一个 js 包。


	这个打包环节其实是想用 webpack 打一个 umd 的包就得了，但是一直知道有个 rollup 的工具存在，非常轻量，同时 Vue.js 也是通过 rollup 打包的，所以就想做出一次尝试。以下是阅读文档，结合 awesome rollup 仓库总结的一些经验。


	同时本文只会涉及到一些 rollup 相关的，关于 markdown 处理会再开一篇文章记录。


## 起步


[rollup github 地址](https://github.com/rollup/rollup)


[官方文档](https://rollupjs.org/guide/en/)


[官方提供的构建 library 的模板](https://github.com/rollup/rollup-starter-lib)


[awesome-rollup](https://github.com/rollup/awesome)


[本文涉及到的源码](https://github.com/phshy0607/markdown-preview)


## package.json


```json
{
  "name": "my-package",
  "version": "0.1.0",
  "main": "dist/my-package.js"
}
```


通常，包内的 package.json 通常用有一个叫 main 的属性，他说明的是本包的内容是在`dist/my-package.js`中。


像 Webpack, Browserify 之类的 bundler 会解析这个 main, 然后引入 main 属性对应的 js，同时根据 dependencies 去载入相应依赖。 在第三方打包时一般都会优先选用 umd 的包，因为这样浏览器也可以使用，所以一般第三方的 lib 的 main 属性通常都是 umd 的包。


而 rollup 属于 ES2015-aware 的工具，在 rollup 内使用 CJS 或者 UMD 并不是最理想的情况，因为无法使用 ES6 module 的特性。 如果对方在使用 rollup 作为他的 bundler, 而也说了 rollup 属于 ES2015-aware 的工具，他会优先读取 package.json 里的 module 属性，如果没有，才会读 main 属性，这样既可以保证一般 bundler 可以正常解析文件，同时在使用 rollup 的时候会优先读取 module 里的 es 包。


所以 package.json 就会变成：


```json
{
  "name": "my-package",
  "version": "0.1.0",
  "main": "dist/my-package.umd.js",
  "module": "dist/my-package.esm.js"
}
```


这对于打出的包来说属于双赢。 具体可以查看[官方对于这种做法的解释](https://github.com/rollup/rollup/wiki/pkg.module)


## 配置文件


[配置文件详细文档](https://rollupjs.org/guide/en/#configuration-files)


> rollup 的配置文件可以使用 es6 语法编写 使用-c 来指定 config 文件，默认读取根目录下 rollup.config.js


```shell
rollup -c
rollup --config
rollup -c build/rollup.config.js
```


这里也放出我用的配置文件


```javascript
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "rollup-plugin-commonjs";
import pkg from "./package.json";
import dev from "rollup-plugin-dev";
import postcss from "rollup-plugin-postcss";
import { string } from "rollup-plugin-string";
import { terser } from "rollup-plugin-terser";

const devServerConfig = {
  port: 3000,
};

const plugins = function (format) {
  const commonPlugins = [
    dev(devServerConfig),
    postcss({
      extract: false,
    }),
    string({
      include: "**/*.md",
    }),
    terser(),
  ];
  if (format === "umd") {
    return [...commonPlugins, resolve(), commonjs()];
  } else {
    return commonPlugins;
  }
};

export default [
  // for browser
  {
    input: "src/index.js",
    output: {
      name: "MarkdownPreview",
      file: pkg.browser,
      format: "umd",
    },
    plugins: plugins("umd"),
  },

  // for cjs and esm
  {
    input: "src/index.js",
    external: Object.keys(pkg.dependencies),
    output: [
      { file: pkg.main, format: "cjs" },
      { file: pkg.module, format: "es" },
    ],
    plugins: plugins(),
  },
];
```


## 常用依赖

1. [rollup-plugin-commonjs](https://github.com/rollup/rollup-plugin-commonjs) 支持解析 commonjs 包至 es6 module
2. [@rollup/plugin-node-resolve](https://github.com/rollup/plugins/tree/master/packages/node-resolve) 支持解析 node_modules 里的包 之前的包[rollup-plugin-node-resolve](https://github.com/rollup/rollup-plugin-node-resolve)已经 archive 了且不再维护
3. [rollup-plugin-vue](https://github.com/vuejs/rollup-plugin-vue)
4. [rollup-plugin-jsx](https://github.com/konsumer/rollup-plugin-jsx)
5. [rollup-plugin-md](https://github.com/xiaofuzi/rollup-plugin-md)
6. [rollup-plugin-terser](https://github.com/TrySound/rollup-plugin-terser)
7. [rollup-plugin-string](https://github.com/TrySound/rollup-plugin-string)

## 坑

1. [rollup-plugin-uglify](https://github.com/TrySound/rollup-plugin-uglify) 因为之前没有用过[terser](https://github.com/terser/terser)作为 minify 的工具，所以选择了 rollup-plugin-uglify 打包 esm 的时候竟然不支持 es6 语法。terser 官方也说了这个问题，

> Why choose terser? uglify-es is no longer maintained and uglify-js does not support ES6+. terser is a fork of uglify-es that mostly retains API and CLI compatibility with uglify-es and uglify-js@3.


所以使用 rollup-plugin-terser 作为 minify 工具。

