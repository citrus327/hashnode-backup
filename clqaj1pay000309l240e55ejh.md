---
title: "记一次 Electron 开发全过程"
datePublished: Sat Sep 14 2019 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqaj1pay000309l240e55ejh
slug: electron

---

## 引子

有一个需要开发 PC 桌面端的需求。日常中使用的网易云音乐，Twitch client 等都属于 electron 的产品。 Electron 对于前端开发来说上手非常简单，虽早有耳闻 electron 的强大，也看过一些文档和基本实现，但是没有完整的搭建、开发和维护过一个 electron 项目。借这个机会，熟悉一下 electron 技术栈，也写下本文，做一次技术积累。

## Electron

Electron 分为 Main 进程（主进程）和 Renderer 进程（渲染进程）

主进程为 Node.js 服务端环境，负责数据库处理，文件处理等

渲染进程为浏览器环境

主进程会将渲染进程的 html，创建 window，挂载 html 运行

主进程与渲染进程通过 electron 包提供的 ipcMain 和 ipcRenderer 进行进程通讯。其实就是一个 EventEmitter，发布订阅模式。

## 项目搭建

### 起步

项目开始基本跟着首页的[文档](https://electronjs.org/docs/tutorial/development-environment#setting-up-windows)一步步走。

### 脚手架

直到[Boilerplates and CLIs](https://electronjs.org/docs/tutorial/boilerplates-and-clis)这一章。这一章开头也提到了说 Electron 只是提供了一种基于 chromium 渲染的桌面端包装而已。对于如何开发、构建、打包等是“unopinionated”。这一章中提到了很多的 boilerplate 和 CLI 的方案。

1. `electron-forge`
    
2. `electron-builder`
    
3. `electron-react-boilerplate`
    

前两者是比较基本的脚手架。不限制 renderer 部分选型。`electron-react-boilerplate`这个从名字看就基本限定死 React 了。由于时间紧迫，React 的水平也确实有限。只能在前两者里挑选。

对于前两个脚手架，我选的是 star 数较多的 electron-builder，时间有限，仅能基于 star 数选型了。

### electron-builder

使用`yarn add electron-builder --dev`安装。同时 electron-builder 本身也提供了一些[模板项目](https://www.electron.build/#boilerplates)供快速开发参考。

这里的我使用[vue-cli-plugin-electron-builder](https://nklayman.github.io/vue-cli-plugin-electron-builder/)进行项目搭建。这个是基于 Vue CLI3 的插件。关于 Vue CLI3 的安装和插件的安装在此不做赘述。

### vue-cli-plugin-electron-builder 插件配置

由于这个插件是 Vue CLI 的插件，那配置文件理所应当在 vue.config.js 内。 贴上我目前的项目配置：

```jsx
module.exports = {
  pluginOptions: {
    electronBuilder: {
      builderOptions: {
        appId: "com.example.app",
        productName: "test-app",
        directories: {
          buildResources: "./build",
          output: "./dist",
        },
        win: {
          //win相关配置
          icon: "./build/icon.png", //图标，当前图标在根目录下，注意这里有两个坑
          target: [
            {
              target: "nsis", //利用nsis制作安装程序,
              arch: [
                "x64", //64位
                "ia32", //32位
              ],
            },
          ],
        },
        nsis: {
          allowToChangeInstallationDirectory: true,
          oneClick: false,
        },
      },
    },
  },
};
```

## 项目结构

1. build 文件夹 存放 打包需要的文件，例如 icon 等
    
2. public 文件夹
    
    * 静态文件夹
        
    * 包含渲染进程 favcon.ico,index.html 等静态文件
        
3. src 文件夹
    
    * 项目代码
        
    * assets: 渲染进程静态文件夹
        
    * biz: 主进程业务代码
        
    * components: Vue.js 组件
        
    * plugins: Vue.js 插件
        
    * router: Vue-router
        
    * store: Vuex
        
    * views: Vue.js 主视图
        
    * background.js: 主进程入口
        
    * main.js 渲染进程入口
        
4. vue.config.js
    
    * vue-cli3 的配置文件
        

## 主进程与渲染进程通讯

由于 electron 是一个 unopinioned 工具，他仅仅提供了进程通讯的方式，而对于如何组织通讯代码，如何封装 API 是没有一个最优解的。在这我会放出我对于进程通讯的二次封装。

### 概念描述

首先定义几个概念

1. API
    
    * 应用程序接口。
        
    * 这里的 API 是给调用方使用的，也就是渲染进程。
        
2. Payload Type
    
    * 荷载类型。
        
    * 定义业务类型（事件类型），其实也就是潜在的定义了业务入参和负责业务的具体描述。
        
3. Routes
    
    * 路由。
        
    * 分发绑定业务句柄和业务类型。
        
4. Services
    
    * 服务。
        
    * 纯净的业务处理，会涉及业务数据处理，db 操作等。
        

### 路由与业务绑定

先放出 Payload Type 的定义

```jsx
const PAYLOAD_TYPES = Object.freeze({
  FETCH_FULL_DATA: "FETCH_FULL_DATA",
  FETCH_DROPDOWN_LIST: "FETCH_DROPDOWN_LIST",
  UPDATE_CELL: "UPDATE_CELL",
  SORT_SHEET: "SORT_SHEET",
  VALIDATE_SHEET: "VALIDATE_SHEET",
  CALCULATE_SHEET: "CALCULATE_SHEET",
  FETCH_INITIAL_DATA: "FETCH_INITIAL_DATA",
  DELETE_ROW: "DELETE_ROW",
});
```

Routes 会基于 Payload Type 进行类型和句柄的绑定，句柄内调用 Services 进行业务处理。

```jsx
import { PAYLOAD_TYPES } from './constants'
import * as service from '../service'

export default {
  [PAYLOAD_TYPES.FETCH_FULL_DATA]: function (payload, callback) {
    callback(service.fetchFullData(payload))
  },
  [PAYLOAD_TYPES.FETCH_DROPDOWN_LIST]: function (payload, callback) {
    callback(service.fetchDropdownList(payload))
  },
  [PAYLOAD_TYPES.UPDATE_CELL]: function (payload, callback) {
    callback(service.updateCell(payload))
  }
```

绑定路由至主进程

```jsx
const establishConnection = () => {
  Object.keys(routes).forEach((routeName) => {
    ipcMain.on(routeName, (e, payload) => {
      routes[routeName](payload, (respond) => {
        e.sender.send(routeName, respond);
      });
    });
  });
};
```

API 声明

```jsx
import { ipcRenderer } from "electron";
import { PAYLOAD_TYPES } from "../channel/constants";
export default function fetchFullData(sheetName) {
  return new Promise((resolve, reject) => {
    // 定义请求参数
    const payload = {
      sheetName,
    };

    // 发送请求
    ipcRenderer.send(PAYLOAD_TYPES.FETCH_FULL_DATA, payload);

    // 接受响应给callback
    ipcRenderer.once(PAYLOAD_TYPES.FETCH_FULL_DATA, (e, res) => {
      resolve(res);
    });
  });
}
```

以上操作之后，API 部分只要处理 payload 参数，发送到对应 payload type 的频道和绑定相应类型的响应句柄就可以了。

这样 API 的入参很清晰，回参就是 service 的回参。一番映射之后，API 和 Services 的逻辑就相当纯净了，无需考虑 ipcMain 和 ipcRenderer 等一系列 Electron 相关的通讯操作。

新增 API 也只需要定义 payload type，定义 api，映射 payload type 和句柄，调用 service 就行了。

这套设计的优点在于，基本 API, Routes，Service 的创建只需要 copy 过去就能行，改改 payload 和方法名称，设计一下入参即可。之后的开发就可以完全沉浸在 Services 这一层里。

这套设计借鉴了 Redux 和 Node.js 里很多框架都采用的中心化路由映射的思想。

## 数据存储

数据存储的话由于数据比较简单，目前我遇到的业务需求，基本一个 mongodb 就能够搞定。但是由于是 electron，肯定不希望往使用方电脑装太多东西，其实 mongodb 也就是个 json。那只需要找一个简单、成熟、语法友好的基于 json 的本地持久化工具即可。

在这里使用[lowdb](https://github.com/typicode/lowdb)

### 数据持久化

db 文件放在 user data 里做持久化。 获取 user data 的方法：[app.getPath()](https://electronjs.org/docs/api/app#appgetpathname)

```jsx
const filePath = path.join(app.getPath("userData"), "/data.json");
```

### lowdb

lowdb 是基于 lodash API 的 json 持久化工具。前端做久了，Lodash 肯定是知道的，语法也比较熟。在这放出一些简单的 db 操作

### 初始化

```jsx
const low = require("lowdb");
const FileSync = require("lowdb/adapters/FileSync");

const filePath = path.join(app.getPath("userData"), "/data.json");
const adapter = new FileSync(filePath);
const db = low(adapter);
```

### db 操作-增删改查

增

```jsx
db.get("posts").push({ title: "example" }).write();
```

删

```jsx
db.get("posts").remove({ title: "example" }).write();
```

改

```jsx
db.get("posts").find({ title: "example" }).assign({ title: "hi!" }).write();
```

查

```jsx
db.get("posts").value();
```

## 渲染进程

因为是基于 Vue CLI 搭的项目，那渲染进程这边肯定是选用 Vue.js 作为前端选型。到这里基本就跟普通 web 前端开发一样了。 业务就调用 API 就行了。

## 打包

electron-builder 提供了很多的配置项。参考[https://www.electron.build/configuration/configuration](https://electronjs.org/docs/tutorial/development-environment#setting-up-windows)

配置文件也已经在上文中给出。

## 总结

整个项目从构思到搭建，再到开发流程基本没采坑，主要还是因为文档比较给力。

感觉唯一学到的东西就是终于有一套构建桌面端的解决方案了。。

如果有后续更新会在此补充，感谢阅读。