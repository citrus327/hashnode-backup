---
title: "Typescript React组件文档自动生成引发的问题"
datePublished: Sun Jan 02 2022 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajedbi000608jreup1elud
slug: typescript-react
tags: reactjs, typescript

---

> 背景: 使用react-typescript-docgen直接去 parse 我写的一个组件无效。

直接上代码

```jsx
import React from 'react'

export interface ButtonProps {
  children?: React.ReactNode
  /** prop1 description */
  onClick: () => void
}

const Button: React.FunctionComponent<ButtonProps> = (props: ButtonProps) => {
  return <button>123</button>
}

export default Button
```

parser 代码

```jsx
const docgen = require("react-docgen-typescript");

const options = {
  savePropValueAsString: true,
};
// Parse a file for docgen info
const result = docgen.parse("./test/Button.tsx", options);

console.log(result);
```

从源码中找到了 getComponentInfo 的代码

```jsx
// node_modules/react-docgen-typescript/lib/parser.js
Parser.prototype.getComponentInfo = function (
  exp,
  source,
  componentNameResolver,
  customComponentTypes
) {
  // ...
  var defaultComponentTypes = [
    "__function",
    "StatelessComponent",
    "Stateless",
    "StyledComponentClass",
    "StyledComponent",
    "FunctionComponent",
    "ForwardRefExoticComponent",
  ];
  var supportedComponentTypes = __spreadArray(
    __spreadArray([], defaultComponentTypes),
    customComponentTypes
  );
  // ...
};
```

发现这里默认的`componentTypes`只有 `FunctionComponent` ，没有 `React.FunctionComponent`, 于是将代码改成了`import { FunctionComponent } from 'react'` 果然可以了！

我第一反应是这作者有点啥，为啥会做这种约定，我更倾向写`React.FunctionComponent`的，虽然累赘但是表意很清晰。

不过紧跟着发现是可以通过 customComponentTypes 这个文档上压根没有的属性进行额外拓展，于是在使用 options 去拓展了 customComponentTypes 这个设置。

```jsx
const docgen = require("react-docgen-typescript");

const options = {
  customComponentTypes: ["React.FunctionComponent"],
};

// Parse a file for docgen info
const result = docgen.parse("./test/Button.tsx", options);

console.log(result);
```

通过 debug 发现，这个`customComponentTypes`确实被成功使用了，但是问题还是没有解决。

我又去重新阅读了官方的文档，发现他们在 ts 中使用`react`，都是`import * as React from 'react'`

我就将我的 demo 改成了

```jsx
import * as React from 'react' // 这行

export interface ButtonProps {
  children?: React.ReactNode
  /** prop1 description */
  onClick: () => void
}

const Button: React.FunctionComponent<ButtonProps> = (props: ButtonProps) => {
  return <button>123</button>
}

export default Button
```

去掉了`customComponentTypes`设置，重新跑一遍，组件被成功 parse 了。

这其实问题就转变成了`import React from 'react'` 和 `import * as React from 'react'`的问题。

想想其实我的运行环境是 node 环境，也就是 cjs.

React 本身编译后的 cjs 包，就是没有 default 属性的，所以在 ts 环境下需要加入`{esModuleInterop: true}`。

通过增加 esModuleInterop 为 true，问题成功得到解决。

参考：

* [https://stackoverflow.com/questions/54585763/what-is-the-difference-between-import-as-react-from-react-vs-import-react-fr](https://stackoverflow.com/questions/54585763/what-is-the-difference-between-import-as-react-from-react-vs-import-react-fr)
    
* [https://github.com/styleguidist/react-docgen-typescript](https://stackoverflow.com/questions/54585763/what-is-the-difference-between-import-as-react-from-react-vs-import-react-fr)
    
* [https://zhuanlan.zhihu.com/p/148081795](https://stackoverflow.com/questions/54585763/what-is-the-difference-between-import-as-react-from-react-vs-import-react-fr)