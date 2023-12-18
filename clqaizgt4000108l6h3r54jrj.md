---
title: "rollup 打包 _export is not defined 问题解决方案"
datePublished: Mon Dec 18 2023 06:18:56 GMT+0000 (Coordinated Universal Time)
cuid: clqaizgt4000108l6h3r54jrj
slug: rollup-export-is-not-defined
tags: javascript, rollup

---

## Rollup 配置

```javascript
import { babel } from "@rollup/plugin-babel";
import commonjs from "@rollup/plugin-commonjs";
import resolve from "@rollup/plugin-node-resolve";
import url from "@rollup/plugin-url";
import external from "rollup-plugin-peer-deps-external";
import postcss from "rollup-plugin-postcss";
import typescript from "rollup-plugin-typescript";
import pkg from "./package.json";

const config = {
  input: "src/index.ts",
  output: [
    {
      file: pkg.main,
      format: "cjs",
      exports: "named",
    },
    {
      file: pkg.module,
      format: "es",
      exports: "named",
    },
  ],
  plugins: [
    resolve(),
    commonjs(),
    postcss({
      plugins: [],
      minimize: true,
    }),
    external({
      includeDependencies: false,
    }),
    babel({
      babelHelpers: "bundled",
      presets: [
        [
          "@babel/preset-env",
          {
            useBuiltIns: "usage",
            corejs: 3,
          },
        ],
      ],
    }),
    url(),
    typescript({ tsconfig: "./tsconfig.json" }),
  ],
};

export default config;
```

## 问题

报出 `$$1w is not defined`，实际查证是$$1w 指向了`_export`这个方法。而`_export`的定义在最下方。

## 原因

根据以下 Issue

1. [https://github.com/rollup/plugins/issues/466](https://github.com/rollup/plugins/issues/466)
    
2. [https://github.com/rollup/rollup/issues/3802](https://github.com/rollup/plugins/issues/466)
    

原因是当打包 dependencies 时，core-js 本身会被 babel 再解析一遍，造成循环引用。

Quote:

> You need to exclude core-js from babel or @babel/preset-env with useBuiltins: ‘usage’ will create a circular reference trying to process core-js itself:

## 解决方案

1. external 插件配置`includeDependencies: true`, 产物不打包 core-js
    
2. babel 插件 exclude core-js
    

## 结果

```javascript
external({
  includeDependencies: false,
}),
babel({
  babelHelpers: "bundled",
  exclude: [/core-js/],
  presets: [
    [
      "@babel/preset-env",
      {
        useBuiltIns: "usage",
        corejs: 3,
      },
    ],
  ],
});
```

或者

```javascript
external({
  includeDependencies: true,
}),
babel({
  babelHelpers: "bundled",
  presets: [
    [
      "@babel/preset-env",
      {
        useBuiltIns: "usage",
        corejs: 3,
      },
    ],
  ],
});
```