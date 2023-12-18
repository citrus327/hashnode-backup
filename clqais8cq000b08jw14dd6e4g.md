---
title: "VS Code 中使用 Webpack alias 后代码的 Import 与 eslint 兼容"
datePublished: Sun Aug 09 2020 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqais8cq000b08jw14dd6e4g
slug: vs-code-webpack-alias-import-eslint
tags: javascript

---

在项目中使用基于 airbnb 的 lint 规则，但配置完 alias 后，出现无法跳转，import/no-resolved 规则报错等问题，调研几个小时，最终得出了解决方案，在此记录分享。

## 方案

关于跳到引入文件位置的问题：

```json
// jsconfig.json
{
  "compilerOptions": {
    "jsx": "react", // 支持jsx后缀
    "module": "commonjs", // commonjs规则，自动使用index.js作为入口
    "target": "es6",
    "paths": {
      // alias配置
      "@/*": ["src/*"]
    },
    "baseUrl": "./" // 配合paths
  }
}
```

很多人会忽略这个 jsconfig 配置，仅配置 paths 和 baseUrl。若仅配置 paths 和 baseUrl，使用 ctrl + left click 将无法定位到引入文件位置，jsx 和 module 的配置很关键。

关于 eslint：

若使用 airbnb 的 lint 规则或者使用 eslint-import-plugin，会无法识别 webpack 或者工程内的 alias 编译配置，关于这个问题可以使用相应的[resolvers 配置](https://github.com/benmosher/eslint-plugin-import/blob/master/README.md#resolvers)。

可以使用 eslint-import-resolver-webpack 这个包作为 resolver 配置，例

```jsx
// eslintrc.js
module.exports = {
  // ...
  settings: {
    // ...
    "import/resolver": {
      webpack: {
        config: "./config/webpack/webpack.base.js",
      },
    },
  },
  // ...
};
```

这里的问题在于如果使用 umi/cra/icejs 等三方 boilerplate 搭建出的项目，可能没有 webpack config，这里你会需要创建一个假的 webpack 配置，或者直接写 webpack 配置到 eslintrc.js

如果想绕过这个 webpack 配置，可以使用 eslint-import-resolver-custom-alias 这个包，配置如下

```jsx
// eslintrc.js
module.exports = {
  // ...
  settings: {
    // ...
    "import/resolver": {
      "eslint-import-resolver-custom-alias": {
        alias: {
          "@": "./src",
        },
        extensions: [".js", ".jsx"],
      },
    },
  },
  // ...
};
```

以上两个方案推荐使用 eslint-import-resolver-custom-alias，简单方便且脱离 webpack