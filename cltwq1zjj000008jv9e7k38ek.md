---
title: "前端面试知识库（2023）"
datePublished: Mon Mar 18 2024 09:06:54 GMT+0000 (Coordinated Universal Time)
cuid: cltwq1zjj000008jv9e7k38ek
slug: 2023-1

---


> 本篇涵盖了从网上搜刮的面试题和自己平时理解不太深刻的问题，作为一种知识积累的方式。


## HTML


### HTML5 新特性

1. 语义化元素
2. 一些新的属性 `contentediable`，`spellcheck` 等
3. input 的新类型：`date`, `email`, `url` 等

### `<meta/>`的作用

1. seo 优化
2. `viewport` 设置手机端适配
3. 设置 charset 字符编码

```html
<meta name="keywords" content="电商,物流" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<meta charset="utf-8" />
```


### CSS, JS与页面渲染的关系？


**先给结论：**

1. css加载不会阻塞DOM树解析，但是会阻塞DOM树渲染，参考：[css加载会造成阻塞吗](https://segmentfault.com/a/1190000018130499)
2. css会阻塞 JS的执行

实际情况是不同的浏览器可能会有不同的执行结果，因为浏览器引擎的不同可能会导致执行结果的不同。

- **webkit渲染过程**

	![Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/11f66c5f-6043-406b-b492-4dc267645668/78a70eaf-0bf0-4a22-b0ec-6c206078393f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45HZZMZUHI%2F20240318%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20240318T090625Z&X-Amz-Expires=3600&X-Amz-Signature=0ec67ac53d5f964a0d0ab22527f630cd5e8886e0331fdd6a50c62ff93e3eb9d8&X-Amz-SignedHeaders=host&x-id=GetObject)

- **Gecko渲染过程**

![Untitled.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/11f66c5f-6043-406b-b492-4dc267645668/c520d268-cd99-4a4a-9f9d-24af821fc453/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45HZZMZUHI%2F20240318%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20240318T090619Z&X-Amz-Expires=3600&X-Amz-Signature=6d2a0d87bfe63eea996fca77ecd760a039ec4f7ea60daf2f81931207821f53f4&X-Amz-SignedHeaders=host&x-id=GetObject)

1. DOM解析和CSS解析是两个并行的进程，所以这也解释了为什么CSS加载不会阻塞DOM的解析。
2. 然而，由于Render Tree是依赖于DOM Tree和CSSOM Tree的，所以他必须等待到CSSOM Tree构建完成，也就是CSS资源加载完成(或者CSS资源加载失败)后，才能开始渲染。因此，CSS加载是会阻塞Dom的渲染的。
3. 由于js可能会操作之前的Dom节点和css样式，因此浏览器会维持html中css和js的顺序。因此，样式表会在后面的js执行前先加载执行完毕。所以css会阻塞后面js的执行。

### FOUC问题

- 解决字体FOUC：[https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)
- preload css
- 有意的先隐藏文档内容，`DomContentLoaded`事件后再显示

### Worker


为了利用多核 CPU 的计算能力，HTML5 提出 Web Worker 标准，允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM。所以，这个新标准并没有改变 JavaScript 单线程的本质。


### 图片懒加载怎么实现


**思路：**

1. 判断 img 标签是否在可视区域，如果可视，加载图片，否则不加载。
2. 因为 src 如果有值，会直接加载，所以可以使用 data-src 属性存放图片地址，等到图片在可视范围内，再把值 apply 到 src 上。

**解法：**


判断可视区域可以用 offset 自己去算，或者是用[element.getBoundingClientRect](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)去获取 dom 位置。


但是这些都会触发 reflow, 有个 API 叫做[IntersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver)，可以动态的监测 dom 是否可视。


### repaint 重绘和 reflow 回流


**Repaint:**

- dom 元素的视觉效果改变，但是不涉及任何排版布局改变
- 主要针对单一元素的重绘
- 例如：color 修改，text-align，hover 引起的颜色变化以及任何不改变 dom 结构变化的变化

**Reflow:**

- dom 元素的位置，结构变化
- 因为这些变化会让浏览器重新计算 dom 元素所在位置和所占空间， 这些变化大多针对整个页面，所以 reflow 要比 repaint 消耗的多
- 例如：
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
	- 默认为 content-box，width 和 height 指定为 content 的高宽
	- border-box, width 和 height 会为 border+padding+content 的高宽

### 元素居中

1. 行元素
	- 单行行元素，可以 height = line-height 或者固定上下 padding
	- flex
2. 块元素
	- 子元素 absolute 定位，top:50%, transition: translateY(-50%)
	- 父元素 table, 子元素 table-cell, vertical-align: middle
	- flex
3. 全居中
	- absolute + top: 50% + left: 50% + translate(-50%, -50%)
	- flex
	- 父容器要求有高度 position: absolute; top: 0; left: 0; right: 0; bottom: 0; margin: auto;

### `Position` 值

1. `static` 是默认值
2. `relative` 相对定位 相对于自身原有位置进行偏移，仍处于标准文档流中
3. `absolute` 绝对定位 相对于最近的已定位的祖先元素, 有已定位(指 position 不是 static 的元素)祖先元素, 以最近的祖先元素为参考标准。如果无已定位祖先元素, 以 body 元素为偏移参照基准, 完全脱离了标准文档流。
4. fixed 固定定位的元素会相对于视窗来定位,这意味着即便页面滚动，它还是会停留在相同的位置。一个固定定位元素不会保留它原本在页面应有的空隙。

### css 优先级

- 内联样式权重：1000
- id 选择器权重：0100
- 类选择器，属性选择器，伪类选择器权重：0010
- 元素选择器，伪元素选择器权重：0001
- 通配选择器 *，子选择器 >，相邻选择器 +。权重：0000

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

1. 若你想在浏览器下次重绘之前继续更新下一帧动画，那么回调函数自身必须再次调用 `window.requestAnimationFrame()`
2. `window.requestAnimationFrame(callback)`回调函数内参数是一个时间戳为 Performance.now()
3. 返回一个动画 ID，可以使用`window.cancelAnimationFrame()`取消

```javascript
const $box = document.getElementById("box");
function myAnimation(timestamp) {
	// 每次调用的时候，都把宽度+1
  $box.style.width = `${$box.clientWidth + 1}px`; 
	// 调用requestAnimationFrame去执行本函数，每次执行完之后，会继续执行动画
  requestAnimationFrame(myAnimation); 
}
myAnimation();
```


### BFC


BFC [Block formatting context](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Block_formatting_context)是一个类似独立的隔离容器（脱离文档流），floating 可以创建 BFC. float 的元素会脱离文档流，如果他的父元素也是个 BFC，那他们 2 个元素都会在 BFC 里，就在这个独立的容器内了。 触发条件：

1. overflow 不是 visible
2. 浮动元素
3. 绝对定位元素
4. 非块级元素（inline-block 等）
5. 根元素（html）或其他包含它的元素 BFC 内会触发  边距坍塌，但是 2 个不同的 BFC 就不会触发

`clear: left/clear: right/clear: both` 清除元素 2 边的浮动。例：clear:left 如果施加在某个元素，那么这个元素左边的浮动元素就不是浮动元素了


## JavaScript


### 事件传播


事件捕获阶段，处于目标阶段，时间冒泡阶段 `event.stopPropagation()` 阻止事件继续传播，即取消进一步的事件捕获或冒泡


### 箭头函数与普通函数区别

1. 箭头函数的 this 指向为声明位置的 this 指向，而普通 function 则为由他实例化的对象本身

### for..in和for..of的区别


for..in会遍历原型链上的属性，for..of不会。


for..of可遍历可迭代对象的属性，例如数组字符串Map等


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
2. 如果遇到异步任务
	1. 如果是宏任务，加入 Task Queue
	2. 如果是微任务，加入 Microtask Queue
3. 当前 Call Stack 空栈后，开始清空 Microtask Queue
4. 此时 Call Stack 空栈，Microtask Queue 被清空，轮到 UI renderer
5. 开始执行清空 Task Queue
6. 循环

执行顺序：微任务 >> UI Render >> 宏任务

<details>
<summary>执行顺序说明</summary>

A more detailed event loop algorithm (though still simplified compared to the [specification](https://html.spec.whatwg.org/multipage/webappapis.html#event-loop-processing-model)):

1. Dequeue and run the oldest task from the _macrotask_ queue (e.g. “script”).
2. Execute all _microtasks_:
	- While the microtask queue is not empty:
		- Dequeue and run the oldest microtask.
3. Render changes if any.
4. If the macrotask queue is empty, wait till a macrotask appears.
5. Go to step 1.

</details>

<details>
<summary>为什么要有微任务</summary>

在Promise的概念引入之前是没有微任务的概念的。在引入Promise之前，JavaScript主要使用回调函数来处理异步操作，这导致了回调地狱的问题，使得代码难以阅读和维护。可以说微任务的概念是随着Promise的引入而出现的，它成为一种处理异步任务的更灵活和高效的机制，有助于改善异步代码的质量和可读性


</details>


微任务包括 promise.resolve, promise.catch, MutationObserver


宏任务包括 setTimeout, setInterval, requestAnimationFrame


```javascript
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
// script start - call stack
// async1 start - call stack
// async2 - micro stack
// promise1 - call stack
// script end - call stack
// async1 end - call stack
// promise2
setTimeout
```


### 事件委托


父节点监听子节点事件，调用行为


### 原型链继承，instance of, ES6 ES5 的继承区别, 如何实现一个 new


[类、原型和继承](https://github.com/ziyi2/js/blob/master/JS%E7%B1%BB%E5%92%8C%E7%BB%A7%E6%89%BF.md)


```javascript
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


```javascript
// new 方法实现
function _new(fn, ...arg) {
  const obj = Object.create(fn.prototype);
  const ret = fn.apply(obj, arg);
  return ret instanceof Object ? ret : obj;
}
```


### 手写 promise


[https://github.com/haizlin/fe-interview/issues/1335](https://github.com/haizlin/fe-interview/issues/1335)


### 节流和防抖


```javascript
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


## 浏览器与 HTTP


### http 状态码

- 1** 信息，服务器收到请求，需要请求者继续执行操作
- 2** 成功，操作被成功接收并处理 200
- 3** 重定向，需要进一步的操作以完成请求 304
- 4** 客户端错误，请求包含语法错误或无法完成请求 404
- 5** 服务器错误，服务器在处理请求的过程中发生了错误 500

### OSI 分层

1. 应用层: http 协议，dhcp, ftp, pop3, smtp
2. 表示层: SSL 加密协议
3. 会话层: 会话层的作用就是为创建、管理和终止会话提供必要的方法，还负责管理和确定传输模式 （单向 全双工 半双工）
4. 传输层 TCP 协议 UDP 协议
5. 网络层 IP 协议：ipv4, ipv6
6. 链路层
7. 物理层

### 怎么发送一个跨域的 POST 请求, 同源策略？CSRF/clickjacking? CORS? JSONP? IFrame?


https://github.com/amandakelake/blog/issues/62 CORS（跨域资款共享）


小提示：如果你回答跨域解决方案 CORS，那么面试官一定会问你实现 CORS 的响应头信息 Access-Control-Allow-Origin。


什么是 CORS（跨域资源共享 Cross-origin resource sharing）允许浏览器向跨域服务器发出 XMLHttpRequest 请求，从而克服跨域问题，它需要浏览器和服务器的同时支持。


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

- 主要区别在于从数据完整上，session 安全上等，UDP 不可靠，TCP 可靠。
- TCP 协议在传送数据段的时候要给段标号；UDP 协议不要
- TCP 协议可靠；UDP 协议不可靠
- TCP 协议是面向连接；UDP 协议采用无连接
- TCP 协议负载较高，采用虚电路；UDP 采用无连接
- TCP 协议的发送方要确认接收方是否收到数据段（3 次握手协议）
- TCP 协议采用窗口技术和流控制

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


[Reference](https://blog.csdn.net/qzcsu/article/details/72861891)


### 浏览器的缓存机制


分为强缓存和协商缓存


[https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching#概览](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching#%E6%A6%82%E8%A7%88)


缓存机制可能存在的 3 种状态，缓存命中，缓存未命中，缓存验证再命中（验证缓存新鲜度）

1. 强缓存 200
	- 利用 http 头里的 expires 或 cache-control 两个字段控制
	- 这 2 个字段是在响应头里，也就是由浏览器本身验证
	- 当请求发出时，浏览器会判断目标资源是否命中强缓存
	- 如果命中，则直接取缓存，否则发请求
	- expires 与时间戳强耦合，所以其实会出现问题。cache-control 就是解决这个问题的。
	- cache-control 允许传入 max-age: 300000，因为传入的是时间段，所以解决了 expires 的时间戳耦合问题
	- Cache-Control 相对于 expires 更加准确，它的优先级也更高。当 Cache-Control 与 expires 同时出现时，我们以 Cache-Control 为准
		- cache-control 的一些细节描述：
			1. no-store: 一切响应不缓存
			2. no-cache: 发送缓存之前，强制要求向原始服务器验证
			3. must-revalidate: 一旦资源过期，成功向服务器验证之前，缓存不能用该资源响应后续请求
2. 协商缓存 304
	- 协商缓存机制下，浏览器需要向服务器去询问缓存的相关信息，进而判断是重新发起请求、下载完整的响应，还是从本地获取缓存的资源
	- 肯定会访问服务器，所以是由服务器决定是否缓存。
	- if-modified-since 这个请求头会发送时间戳到服务器，服务器验证是否过期，如果没有过期，返回 304
	- 如果过期了，会返回 expires 响应头和 200 状态码。下次请求 if-modified-since 就会以这个 expires 的时间戳再次请求。

优劣：

1. 协商缓存每次都会访问服务器
2. 强缓存如果服务器资源更新，可能会访问老接口，如果老接口下架了，就 gg 了

缓存的意义在于减少服务器请求，更多使用本地资源。所以要让每次文件更新之后，让缓存失效。所以可以使用 hash。 html 使用协商缓存，js css 使用强缓存


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

```javascript
if (req.headers.referer !== "http://www.c.com:8002/") {  
	res.write("csrf 攻击");  return;
}
```

1. csrf 能成功是因为能够  截取用户的 Cookie，要抵御这种攻击，可以在请求中带入一个参数，后端进行校验。 例如, jwt

### cookie token 优劣

1. 首先 token 不是防止 XSS 的，而是为了防止 CSRF 的；
2. CSRF 攻击的原因是浏览器会自动带上 cookie，而浏览器不会自动带上 token

[Reference](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/31)


### WebSocket

1. 基于 TCP 的一个全双工通信协议，HTML5 的新特性
2. onclose, onerror, onmessage, onopen
3. socket.close(); socket.send()
4. 兼容性 ie >= 10

## React


### Fiber and R**econciliation**


[https://github.com/acdlite/react-fiber-architecture?tab=readme-ov-file](https://github.com/acdlite/react-fiber-architecture?tab=readme-ov-file)


一个React的核心逻辑重写


**老核心逻辑**

- 递归整棵vdom tree。有数据变化后，更新整颗树，且立即执行。
- 跟正常的callStack一致。
- 同步

**新的Fiber架构**

- Incremental Rendering + Scheduling
- 异步

**新的架构目标**

- 首先，React认为UI渲染是存在优先级的，部分UI更新是可以被滞后甚至废弃的。
- 架构的主要目标就是为了scheduling，即：切分渲染工作至N个小份，并按优先级，且分布在各个帧完成渲染。
- 提供一套机制
	- 暂停work，并推后执行（pause work and come back to it later.）
	- 为不同类型的work设置优先级（assign priority to different types of work.）
	- 重用之前已完成的work（reuse previously completed work.）
	- 废弃无用的work（abort work if it's no longer needed）

**名词解释**

	- 什么是Fiber?
		- 一个fiber就是一个unit of work。
		- 一个plain JavaScript object
	- 什么是scheduling?
		- 一个决定work是否要被执行的过程。
	- 什么是work?
		- 一个必须要被执行的计算过程。一般为一个渲染结果的更新。
		- 一个work的建立可能从： 「state change」「lifecycle fn」「DOM变更」
	- 什么是reconciliation？
		- 一个React内部使用的算法，用于diff两颗树之间的区别

**前提**


React核心思想为`view = fn(data)`


**执行**

- 传统的计算机执行逻辑为使用callStack的概念进行任务编排并执行。每次有新的任务就推一个stackFrame至callStack并按顺序执行。但是在UI领域，例如动画，如果很多work同时执行，会导致画面的卡顿。若利用requestIdleCallback和requestAnimationFrame来解决优先级问题，则需要自行定义自己的stackFrame和callStack来保证每个work可中断或持续进行。
- 一个fiber可以理解成一个visual stack frame。fiber的建立可以帮助到「保持work在内存内的持续存在」「可控制的执行时机」。
- fiber总是和”元素”建立1-to-1关系。这个元素由fiber内部的tag属性来定义，是一个0-24的变量，即FunctionComponent, HostComponent，Fragment, MemoComponent等等。
- Fiber跟React Component有一定的关系，「fiber几乎都是从React Component建立的，甚至共享了一部分数据」「当React Component重建，Fiber可能会被重用」「大多数Fiber是在组件第一次mount的时候建立」
- fiber会被2个阶段分别执行，「渲染阶段」和「提交阶段」。渲染阶段为异步执行，提交阶段为同步执行
	- P.S，即生成fiber + 提交执行
- 多个fiber会组成fiber tree。通过child（指向子节点）, sibling（指向兄弟节点）, return（指向父节点）来表达fiber之间的关系。类似双向链表
- fiber tree有2颗，一颗为`current`，一颗为`workInProgress`。`current`树指当前屏幕上显示的，`workInProgress`树会在后台执行计算，计算完成后替换`current`树
- Effects。当current树和workInProgress树替换时，需要保证effects的执行，可理解为纯函数中的副作用。
- 当fiber tree被执行时，会从上至下开始执行`beginWork`，再从下往上执行`completeWork`。执行顺序为child → sibling → return
- 整体执行是由一个while loop完成。

### Hooks


[https://www.youtube.com/watch?v=1VVfMVQabx0](https://www.youtube.com/watch?v=1VVfMVQabx0)


## 网上面试题集合

1. [https://zhuanlan.zhihu.com/p/671899967?utm_id=0](https://zhuanlan.zhihu.com/p/671899967?utm_id=0)
