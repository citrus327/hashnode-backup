---
title: "2019 年底面试题整理"
datePublished: Tue Nov 26 2019 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqaj8l20000309l8bxvsdmpu
slug: 2019
tags: interview, javascript

---

> 本篇涵盖了从网上搜刮的面试题和自己平时理解不太深刻的问题，作为一种知识积累的方式。

## HTML

### HTML5 新特性

1. 语义化元素
    
2. 一些新的属性 contentediable，spellcheck 等
    
3. input 的新类型：date, email, url 等
    

### meta 标签的作用

1. seo 优化
    
2. viewreport 设置手机端适配
    
3. 设置 charset 字符编码
    

```html
<meta name="keywords" content="电商,物流" /><meta name="viewport" content="width=device-width, initial-scale=1.0" /><meta charset="utf-8" />
```

### CSS 是否影响 Html 解析，js 解析，html 渲染？

DOM 解析与 CSS 解析是并行的，CSSOM TREE 和 DOM TREE 完成后，会合并成为 render tree。

也是为何 css 解析不影响 html 解析，但是影响 html 渲染。

js 操作可以改变 html 和 css 样式，因为浏览器会维持顺序，所以 css 会阻止后面 js 的执行。

[Reference](https://segmentfault.com/a/1190000018130499)

### onload 事件与 DOMContentLoaded

1. onload 会等待所有的资源加载完成（html, css, js, 图片, 视频等）
    
2. 如果页面中同时存在 css 和 js，并且存在 js 在 css 后面，则 DOMContentLoaded 事件会在 css 加载完后才执行
    
3. 其他情况下，DOMContentLoaded 都不会等待 css 加载，并且 DOMContentLoaded 事件也不会等待图片、视频等其他资源加载。 [Reference](https://segmentfault.com/a/1190000018130499)
    

### Worker

为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。

### CSS 和 JS 的位置会影响页面效率

1. css 不会阻止 html 解析
    
2. css 会阻止 html 渲染（css 加载完成之前，会白屏）
    
3. css 会阻止 js 运行
    

### 用一个 div 模拟 textarea 的实现

给 div 标签加[contenteditable](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/contenteditable)属性就能实现 textarea 的编辑功能了

### 图片懒加载怎么实现

判断 img 标签是否在可视区域，如果可视，加载图片，否则不加载。

因为 src 如果有值，会直接加载，所以是用一个 data-src 存放图片地址，等到图片在可视范围内，再把值 apply 到 src 上。

判断可视区域可以用 offset 自己去算，或者是用[element.getBoundingClientRect](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)去获取 dom 位置。

但是这些都会触发 reflow, 有个 API 叫做[IntersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver)，可以动态的监测 dom 是否可视。

### repaint 重绘和 reflow 回流

Repaint:

* dom 元素的视觉效果改变，但是不涉及任何排版布局改变
    
* 主要针对单一元素的重绘
    
* 例如：1. color 修改，text-align，hover 引起的颜色变化以及任何不改变 dom 结构变化的变化
    

Reflow:

* dom 元素的位置，结构变化
    
* 因为这些变化会让浏览器重新计算 dom 元素所在位置和所占空间， 这些变化大多针对整个页面，所以 reflow 要比 repaint 消耗的多
    
* 例如：
    
    1. margin/padding
        
    2. 修改 dom 节点结构
        
    3. animation/transition 每一帧都会引起 reflow
        
    4. 读取元素某些属性的时候：offsetTop/top 等, getComputedStyle() 这些属性需要依赖一些元素去计算，所以会触发 reflow
        
    5. scroll 页面, resize 页面
        
    6. 字体大小变化
        

### 如何优化 reflow

1. 减少 dom 之间相互依赖，减少 dom 层级
    
2. 少用复杂动画
    
3. 减少使用 css 中的运算式
    
4. 少改 class，特别是子元素很多的
    
5. 批量更新元素样式，然后设置为一个 class，一下附在 dom 上
    

## CSS

### 盒子模型

1. 盒子具有 content, padding, margin, border
    
2. box-sizing:
    
    * 默认为 content-box，width 和 height 指定为 content 的高宽
        
    * border-box, width 和 height 会为 border+padding+content 的高宽
        

### css 绘制平行四边形

父元素 transform: skewX(-45deg);

子元素 transform: skewX(45deg)

### 元素居中

1. 行元素
    
    * 单行行元素，可以 height = line-height 或者固定上下 padding
        
    * 多行行元素可以用 flex
        
2. 块元素
    
    * 子元素 absolute 定位，top:50%, transition: translateY(-50%)
        
    * 父元素 table, 子元素 table-cell, vertical-align: middle
        
    * flex
        

全居中

1. absolute + top: 50% + left: 50% + translate(-50%, -50%)
    
2. flex
    
3. 父要求有高度 position: absolute; top: 0; left: 0; right: 0; bottom: 0; margin: auto;
    

### Postion values

1. static 是默认值
    
2. relative 相对定位 相对于自身原有位置进行偏移，仍处于标准文档流中
    
3. absolute 绝对定位 相对于最近的已定位的祖先元素, 有已定位(指 position 不是 static 的元素)祖先元素, 以最近的祖先元素为参考标准。如果无已定位祖先元素, 以 body 元素为偏移参照基准, 完全脱离了标准文档流。
    
4. fixed 固定定位的元素会相对于视窗来定位,这意味着即便页面滚动，它还是会停留在相同的位置。一个固定定位元素不会保留它原本在页面应有的空隙。
    

### css 优先级

1. 内联样式权重：1000
    
2. id 选择器权重：0100
    
3. 类选择器，属性选择器，伪类选择器权重：0010
    
4. 元素选择器，伪元素选择器权重：0001
    
5. 通配选择器 \*，子选择器 &gt;，相邻选择器 +。权重：0000
    

css 的覆盖顺序跟 css 的样式声明顺序有关 css 声明的越靠后，优先级越高

### 使用 css 实现一个持续的动画效果

```css
#box {
  width: 30px;
  height: 30px;
  background-color: gray;
  animation-name: myAnimation;
  /* 动画名称 */
  animation-duration: 2s;
  /* 动画持续时间 */
  animation-timing-function: ease-in;
  /* 运动函数 */
  animation-delay: 500ms;
  /* 动画起始延迟 */
  animation-iteration-count: infinite;
  /* 动画重复次数 */
  animation-direction: alternate;
  /**  * 动画执行方向  * normal: 每个循环内动画向前循环，换言之，每个动画循环结束，动画重置到起点重新开始，这是默认属性。  * alternate: 动画交替反向运行，反向运行时，动画按步后退，同时，带时间功能的函数也反向  * reverse: 反向运行动画，每周期结束动画由尾到头运行  * alternate-reverse: 反向交替， 反向开始交替  */
}

@keyframes myAnimation {
  20% {
    transform: translateY(20px);
    /* 向下20px */
  }

  50% {
    transform: translateY(40px);
    /* 再向下20px */
  }

  100% {
    transform: translate(40px, 20px);
    /* 再向右移动20px */
  }
}
```

### 使用 js 实现一个持续的动画效果 requestAnimationFrame

1. 若你想在浏览器下次重绘之前继续更新下一帧动画，那么回调函数自身必须再次调用 window.requestAnimationFrame()
    
2. `window.requestAnimationFrame(callback)`回调函数内参数是一个时间戳为 [Performance.now](https://segmentfault.com/a/1190000018130499)()
    
3. 返回一个动画 ID，可以使用`window.cancelAnimationFrame()`取消
    

```jsx
const $box = document.getElementById("box");
function myAnimation(timestamp) {
	// 每次调用的时候，都把宽度+1
  $box.style.width = `${$box.clientWidth + 1}px`; 
	// 调用requestAnimationFrame去执行本函数，每次执行完之后，会继续执行动画
  requestAnimationFrame(myAnimation); 
}
myAnimation();
```

### 左右布局：左边定宽、右边自适应，不少于 3 种方法

1. float left + overflow:hidden
    
2. float + margin:left
    
3. float:left + (float:right, calc)
    
4. flex: 外容器 flex, 左边定宽，右边 flex: 1 撑满
    

### BFC

BFC 是一个类似独立的隔离容器（脱离文档流），floating 可以创建 BFC. float 的元素会脱离文档流，如果他的父元素也是个 BFC，那他们 2 个元素都会在 BFC 里，就在这个独立的容器内了。 触发条件：

1. overflow 不是 visible
    
2. 浮动元素
    
3. 绝对定位元素
    
4. 非块级元素（inline-block 等）
    
5. 根元素（html）或其他包含它的元素 BFC 内会触发 边距坍塌，但是 2 个不同的 BFC 就不会触发
    

`clear: left/clear: right/clear: both` 清除元素 2 边的浮动。例：clear:left 如果施加在某个元素，那么这个元素左边的浮动元素就不是浮动元素了

## JS

### 事件传播

事件捕获阶段，处于目标阶段，时间冒泡阶段 `event.stopPropagation()` 阻止事件继续传播，即取消进一步的事件捕获或冒泡

### 箭头函数与普通函数区别

1. 箭头函数的 this 指向为声明位置的 this 指向，而普通 function 则为由他实例化的对象本身
    

### call 与 apply 的区别

call 有 N 个参数，第一个参数是上下文，之后的参数就会传给目标函数，逐个列出 apply 有 2 个参数，第一个参数是上下文，第二个参数是参数的数组

### 宏事件，微事件

[任务队列](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

[阮老师的 Event Loop 文章](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)

[Philip Roberts: Help, I’m stuck in an event-loop.](https://vimeo.com/96425312)

事件循环：

1. 所有的同步事件都在主线程上运行，形成一个**执行栈 (execution context stack)**
    
2. 如果碰到异步事件，则推入**任务队列（task queue)**
    
3. 一旦**执行栈**同步任务执行完，则会读取**任务队列**，按照一定顺序执行**任务队列**里的方法。
    
4. 循环
    

所以如果想要模拟这个[场景](https://www.jsv9000.app/)，需要一个 Call Stack, Task Queue, Microtask Queue

1. 同步事件进入 Call Stack，直接执行
    
2. 如果遇到异步任务 A. 如果是宏任务，加入 Task Queue B. 如果是微任务，加入 Microtask Queue
    
3. 当前 Call Stack 空栈后，开始清空 Microtask Queue
    
4. 此时 Call Stack 空栈，Microtask Queue 被清空，轮到 UI renderer
    
5. 开始执行清空 Task Queue
    
6. 循环
    

微任务 &gt;&gt; UI Render &gt;&gt; 宏任务

微任务包括 promise.resolve, promise.catch, MutationObserver

宏任务包括 setTimeout, setInterval, requestAnimationFrame

```jsx
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(function () {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function (resolve) {
  console.log("promise1");
  resolve();
}).then(function () {
  console.log("promise2");
});
console.log("script end");
```

### sort

[https://blog.csdn.net/k346k346/article/details/51154786](https://segmentfault.com/a/1190000018130499)

1. 冒泡排序：双层遍历，对比前后两个节点，如果满足条件，位置互换，直到遍历结束。
    
2. 快速排序：去数组中间的那一个数，然后遍历所有数，小于该数的 push 到一个数组，大于该数的 push 到另外一个数组，然后递归去排序这两个数组，最后将所有结果连接起来。
    
3. 选择排序：声明一个数组，每次去输入数组里面找数组中的最大值或者最小值，取出来后 push 到声明的数组中，直到输入数组为空。
    

### js Array

[https://juejin.im/entry/59ae664d518825244d207196](https://segmentfault.com/a/1190000018130499)

### requestAnimatedFrame

[https://github.com/haizlin/fe-interview/issues/1341](https://segmentfault.com/a/1190000018130499)

### addeventlistener 在父节点，监听子节点点击事件 =&gt; 事件委托

### 原型链继承，instance of, ES6 ES5 的继承区别, 如何实现一个 new

[https://github.com/ziyi2/js/blob/master/JS类和继承.md](https://segmentfault.com/a/1190000018130499)

```jsx
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.say = function () {
  console.log(`I'm Person, my name is ${this.name}, my age is ${this.age}`);
};
Person.prototype.yell = function () {
  console.log(`yell`);
};

function Student(name, age, score) {
  Person.apply(this, arguments);
  this.score = score;
}

// 原型继承
Student.prototype = new Person();
// 修改继承来的原型上的constructor为目标Student的constructor
Student.prototype.constructor = Student;

Student.prototype.say = function () {
  console.log(
    `I'm Student, my name is ${this.name}, my age is ${this.age}, my score is ${this.score}`
  );
};
Student.prototype.studentYell = function () {
  console.log(`student yell`);
};

var p = new Person("a person", "22");
var derek = new Student("derek", "21", "321");
derek.say();
derek.yell();
derek.studentYell();
console.log(derek instanceof Person);
console.log(derek instanceof Student);
// p.studentYell()
```

```jsx
// new 方法实现
function _new(fn, ...arg) {
  const obj = Object.create(fn.prototype);
  const ret = fn.apply(obj, arg);
  return ret instanceof Object ? ret : obj;
}
```

### 手写 promise

[https://github.com/haizlin/fe-interview/issues/1335](https://segmentfault.com/a/1190000018130499)

### 节流和防抖

```jsx
function debounce(fn, delay) {
  let timer = null;
  return function () {
    if (timer) {
      console.log("cancel timer");
      clearTimeout(timer);
    }
    timer = setTimeout(() => {
      fn.apply(this, arguments);
    }, delay);
  };
}

function throttle(fn, delay) {
  let canRun = true;
  return function () {
    if (!canRun) {
      return;
    }
    canRun = false;
    fn.apply(this, arguments);
    setTimeout(() => {
      canRun = true;
    }, delay);
  };
}
```

### 实现页面加载进度条

### postMessage

### 实现拖拽功能，比如把 5 个兄弟节点中的最后一个节点拖拽到节点 1 和节点 2 之间

### 手写 parseInt 的实现

### WeakMap Map Set WeakSet

### 深度优先遍历和广度优先遍历, deep clone

## 浏览器与 HTTP

### http 状态码

1. 1\*\* 信息，服务器收到请求，需要请求者继续执行操作
    
2. 2\*\* 成功，操作被成功接收并处理 200
    
3. 3\*\* 重定向，需要进一步的操作以完成请求 304
    
4. 4\*\* 客户端错误，请求包含语法错误或无法完成请求 404
    
5. 5\*\* 服务器错误，服务器在处理请求的过程中发生了错误 500
    

### OSI 分层

1. 应用层: http 协议，dhcp, ftp, pop3, smtp
    
2. 表示层: SSL 加密协议
    
3. 会话层: 会话层的作用就是为创建、管理和终止会话提供必要的方法，还负责管理和确定传输模式 （单向 全双工 半双工）
    
4. 传输层 TCP 协议 UDP 协议
    
5. 网络层 IP 协议：ipv4, ipv6
    
6. 链路层
    
7. 物理层
    

### 怎么发送一个跨域的 POST 请求, 同源策略？CSRF/clickjacking? CORS? JSONP? IFrame?

[https://github.com/amandakelake/blog/issues/62](https://segmentfault.com/a/1190000018130499) CORS（跨域资款共享）

小提示：如果你回答跨域解决方案 CORS，那么面试官一定会问你实现 CORS 的响应头信息 Access-Control-Allow-Origin。

什么是 CORS CORS（跨域资源共享 Cross-origin resource sharing）允许浏览器向跨域服务器发出 XMLHttpRequest 请求，从而克服跨域问题，它需要浏览器和服务器的同时支持。

浏览器端会自动向请求头添加 origin 字段，表明当前请求来源。

服务器端需要设置响应头的 Access-Control-Allow-Methods，Access-Control-Allow-Headers，Access-Control-Allow-Origin 等字段，指定允许的方法，头部，源等信息。

请求分为简单请求和非简单请求，非简单请求会先进行一次 OPTION 方法进行预检，看是否允许当前跨域请求。

### 从输入 URL 到看到页面发生的全过程，越详细越好。

1. 输入网址
    
2. DNS 解析
    
3. 建立 tcp 连接
    
4. 客户端发送 HTPP 请求
    
5. 服务器处理请求
    
6. 服务器响应请求
    
7. 浏览器展示 HTML
    
8. 浏览器发送请求获取其他在 HTML 中的资源。
    

### TCP 和 UDP 的区别

* 主要区别在于从数据完整上，session 安全上等，UDP 不可靠，TCP 可靠。
    
* TCP 协议在传送数据段的时候要给段标号；UDP 协议不要
    
* TCP 协议可靠；UDP 协议不可靠
    
* TCP 协议是面向连接；UDP 协议采用无连接
    
* TCP 协议负载较高，采用虚电路；UDP 采用无连接
    
* TCP 协议的发送方要确认接收方是否收到数据段（3 次握手协议）
    
* TCP 协议采用窗口技术和流控制
    

### 三次握手，四次挥手

三次握手：

A: 发送了

B: 接受了，你能收到么

A: 收到了，ok，我俩连上了

四次挥手:

tcp 是全双工的，就是 A 正在给 B 发信息的同时，B 也在给 A 发信息

A: 我要关了

B: 可能还有一个包要传输，你稍等

B: OK, 我结束了。我这条消息你接到了，就可以关了

A: 我收到了，我关了。

[Reference](https://blog.csdn.net/qzcsu/article/details/72861891) [Reference](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/15)

### 浏览器的缓存机制:强缓存 协商缓存

缓存机制可能存在的 3 种状态，缓存命中，缓存未命中，缓存验证再命中（验证缓存新鲜度）

1. 强缓存 200
    
    * 利用 http 头里的 expires 或 cache-control 两个字段控制
        
    * 这 2 个字段是在响应头里，也就是由浏览器本身验证
        
    * 当请求发出时，浏览器会判断目标资源是否命中强缓存
        
    * 如果命中，则直接取缓存，否则发请求
        
    * expires 与时间戳强耦合，所以其实会出现问题。cache-control 就是解决这个问题的。
        
    * cache-control 允许传入 max-age: 300000，因为传入的是时间段，所以解决了 expires 的时间戳耦合问题
        
    * Cache-Control 相对于 expires 更加准确，它的优先级也更高。当 Cache-Control 与 expires 同时出现时，我们以 Cache-Control 为准
        
        * cache-control 的一些细节描述：
            
            1. no-store: 一切响应不缓存
                
            2. no-cache: 发送缓存之前，强制要求向原始服务器验证
                
            3. must-revalidate: 一旦资源过期，成功向服务器验证之前，缓存不能用该资源响应后续请求
                
2. 协商缓存 304
    
    * 协商缓存机制下，浏览器需要向服务器去询问缓存的相关信息，进而判断是重新发起请求、下载完整的响应，还是从本地获取缓存的资源
        
    * 肯定会访问服务器，所以是由服务器决定是否缓存。
        
    * if-modified-since 这个请求头会发送时间戳到服务器，服务器验证是否过期，如果没有过期，返回 304
        
    * 如果过期了，会返回 expires 响应头和 200 状态码。下次请求 if-modified-since 就会以这个 expires 的时间戳再次请求。
        

优劣：

1. 协商缓存每次都会访问服务器
    
2. 强缓存如果服务器资源更新，可能会访问老接口，如果老接口下架了，就 gg 了
    

缓存的意义在于减少服务器请求，更多使用本地资源。所以要让每次文件更新之后，让缓存失效。所以可以使用 hash。 html 使用协商缓存，js css 使用强缓存

### 浏览器路由实现

### XSS CSRF

XSS: 跨站脚本攻击, 是指攻击者在网站上注入恶意的客户端代码，通过恶意脚本对客户端网页进行篡改，从而在用户浏览网页时，对用户浏览器进行控制或者获取用户隐私数据的一种攻击方式

防范手段：

1. http only cookie, 不允许 js 获取 cookie
    
2. 输入检查，数据过滤
    
3. 输出检查
    
4. 使用 HTTP 头指定内容的类型，使得输出的内容避免被作为 HTML 解析 application/json
    

CSRF，即 Cross Site Request Forgery，中译是跨站请求伪造，是一种劫持受信任用户向服务器发送非预期请求的攻击方式。

CSRF 攻击往往是在用户不知情的情况下构造了网络请求

1. 使用验证码，强制要求用户与 app 交互，最终才能完成请求
    
2. 使用 request header, referer， 如果访问者不是从指定路径来的，则为 csrf 攻击
    

```jsx
if (req.headers.referer !== "<http://www.c.com:8002/>") {  
	res.write("csrf 攻击");  return;
}
```

1. csrf 能成功是因为能够 截取用户的 Cookie，要抵御这种攻击，可以在请求中带入一个参数，后端进行校验。 例如, jwt
    

### cookie token 优劣

1. 首先 token 不是防止 XSS 的，而是为了防止 CSRF 的；
    
2. CSRF 攻击的原因是浏览器会自动带上 cookie，而浏览器不会自动带上 token
    

[Reference](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/31)

### 介绍 HTTPS 握手过程

A: 发送了

B: 接受了，请发送 clientKey

A: 给你我的 KEY

B: ok，收到了

[Reference](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/70)

### websocket

1. 基于 TCP 的一个全双工通信协议，HTML5 的新特性
    
2. onclose, onerror, onmessage, onopen
    
3. socket.close(); socket.send()
    
4. 兼容性 ie &gt;= 10
    

## Vue

### Computed 缓存实现和依赖收集实现

### keep alive 实现

### vdom 实现，differ 算法，key 的作用

* 简单描述一下 Vue.js 的 render 过程
    
    1. Vue 文件会解析成 3 个片段，HTML 会 parse 成 AST
        
    2. 解析成 vdom
        
    3. 映射成真实 dom
        
    4. Mount
        
    5. 如果数据变动，触发 Object.defineProperty 的 setter
        
    6. differ 原始 vdom 和新的 vdom
        
    7. 找到当中的区别, 变更 differ 出来的 dom
        
* vdom:
    
    1. vdom 其实就是代表真实 dom 的轻量 js 对象，他仅仅记录了部分需要的内容
        
    2. 在操作 dom 的时候，可以先用 vdom 去计算到底有多少改动
        
    3. 在一次 dom 操作中，直接进行变动，这样可以减少 dom 操作
        
    4. 操作 vdom 并不是比真实 dom 操作快，毕竟如果已知 dom 的区别，直接操作 dom 肯定比操作 vdom 再映射区别的方式好，实际的原因是 vdom 其实只是充当一个中间层，能够帮助寻找新旧 dom 的区别，具有普适性，可以在可维护性和效率上达到一个平衡。
        
    5. 目前 Vue.js 使用的 vdom lib 是 [snabbdom](https://github.com/snabbdom/snabbdom)
        

总结一下就是为了找到 dom 变化而衍生出来的一种方式。

* differ 算法 [Reference](https://segmentfault.com/a/1190000008782928):
    
    1. 形成新旧 2 个 vnode tree
        
    2. 从根节点往下进行 differ, 同层比较。
        
    3. 如果发现根节点不同，直接替换。否则继续之前的操作，直到整棵树遍历完全。
        
    4. patchVnode 检查节点，patchVnode 中调用 updateChildren 去 differ 子节点
        
    5. updateChildren: + updateChildren 是通过设定头尾 2 个指针，向中间遍历，也就是 oldStartIndex, oldEndIndex, newStartIndex, newEndIndex + 循环条件是 startIndex &lt;= endIndex, 也就是 2 个指针汇聚，则终止遍历
        
        [https://image-static.segmentfault.com/128/689/1286899590-56d41b839c27f\_articlex](https://segmentfault.com/a/1190000018130499)
        

[Reference](https://zhuanlan.zhihu.com/p/61766666)

### 完成一个 Dialog 组件，说 说你设计的思路

### 为什么 Vuex 的 mutation 中不能做异步操作

[官方约定](https://vuex.vuejs.org/zh/guide/mutations.html#mutation-%E5%BF%85%E9%A1%BB%E6%98%AF%E5%90%8C%E6%AD%A5%E5%87%BD%E6%95%B0)action 中可以发送异步请求，而 mutation 不可以。是因为方便开发 debug/devtools 去检测数据的走向。

### Object.defineProperty 和 Proxy

1. Object.defineProperty
    

* Object.defineProperty 对数组无法做到检测
    
* 必须遍历每一个 key
    
* 如果 object 的层级深，需要遍历并对更深层的子对象进行监测
    
* Vue 在监测数组的时候，其实是重写了 Array 的那些原型方法，达到劫持的目的
    

1. Proxy
    

* 针对整个对象的监测，所以不需要遍历
    
* 支持 Array 的监测
    
* Proxy 也需要遍历子元素进行监测
    

### Vue 的父组件和子组件生命周期钩子执行顺序是什么

[https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/128](https://segmentfault.com/a/1190000018130499)

### $nextTick

[https://juejin.im/post/5cd9854b5188252035420a13](https://segmentfault.com/a/1190000018130499) [https://github.com/DDFE/DDFE-blog/issues/24](https://segmentfault.com/a/1190000018130499)

## Webpack

### [https://segmentfault.com/a/1190000016816813](https://segmentfault.com/a/1190000018130499)

### 性能优化方案

1. webpack 分 chunk, 使用 splitChunk，将 vendor.js 和 bundle.js 分开
    
2. vendor.js 可以再根据 node\_module name 再分，利于缓存
    
3. 压缩代码
    
4. 局部引入代码，例如不完整引入 lodash
    
5. 静态代码可以通过字蛛进行字体压缩
    

## references

1. [https://juejin.im/post/5befeb5051882511a8527dbe](https://segmentfault.com/a/1190000018130499)
    
2. [https://juejin.im/post/5bf8fe2ee51d452d705fee3d](https://segmentfault.com/a/1190000018130499)
    
3. [https://juejin.im/post/5ab0da85f265da23866fb9b7](https://segmentfault.com/a/1190000018130499)
    
4. [https://muyiy.vip/question/](https://segmentfault.com/a/1190000018130499)
    
5. [https://juejin.im/post/5d23e750f265da1b855c7bbe](https://segmentfault.com/a/1190000018130499)
    

## Questions:

1.改造下面的代码，使之输出 0 - 9，写出你能想到的所有解法。

```jsx
for (var i = 0; i < 10; i++) {  setTimeout(() => {    console.log(i);  }, 1000);}
```

1. 下面代码输出什么