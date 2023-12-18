---
title: "element.scrollTo 方法使用与兼容"
datePublished: Mon May 21 2018 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajbmjv000n08jt8tukdrxo
slug: elements-scroll-to-polyfill
tags: javascript

---

> 在工作中碰到控制滚动条滚动的需求，原有代码使用 scrollTo 进行滚动，在现代浏览器上都可以正常运行，但是到了 IE11 上却不行了，于是对 scrollTo 做了一层小小的封装。于是在这里分享一下…

## window.scrollTo

关于在 document 上滚动的问题，直接可以使用[window.scrollTo](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)方法，用法如下：

```jsx
var x = 0, y = 100;
window.scrollTo(x, y);
```

在 mdn 上说明了，如果想对 elements 进行滚动条操作，可以使用`element.scrollTop`和`scrollLeft`。 在这里要补充说明一下`element.scrollTo`方法

## element.scrollTo

`element.scrollTo`方法也是无意间发现，mdn 和 caniuse 上是没有对该方法进行说明的。 该方法入参与 window 上的 scrollTo 一致，用法如下：

```jsx
var $el = document.querySelector("#container");
var x = 0,
  y = 100;
$el.scrollTo(x, y);
```

## 兼容性问题

`window.scrollTo`的兼容性如下

![https://user-images.githubusercontent.com/17166940/94903476-76542e80-04cc-11eb-8a1e-a2e3f9e6808d.png](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)

`window.scrollTo`看上去十分理想，但是`element.scrollTo`就没那么理想化了。

[这位小哥](https://github.com/Fyrd/caniuse/issues/4251)测试了 Chrome, Chrome66 支持 element.scrollTo, Chrome48 不支持

经过几个主流浏览器的测试之后，发现 IE 11 下的`element.scrollTo`为`undefined`。

这里我简单封装了一下 scrollTo，做了一个小小的 polyfill。代码如下：

```jsx
/**
 * polyfill for scrollTo on element
 * @param {HtmlElement} el
 * @param {number} x
 * @param {number} y
 */
function scrollTo(el, x, y) {
  if (!el) {
    return;
  }

  if (el.scrollTo) {
    el.scrollTo(x, y);
  } else {
    el.scrollLeft = x;
    el.scrollTop = y;
  }
}
```

这里也简单说明下 scrollLeft 和 scrollTop 做滚动条操作的用法 如果滚动条在容器最底部，假设$el 为容器

```jsx
$el.clientHeight + $el.scrollTop === $el.scrollHeight; // true
```

scrollTop 可以理解为滚动条距离容器顶部的距离，当然其实这里有很大的坑。 具体可以看 mdn 的 scrollTop 描述。

那 scrollLeft 就是滚动条距离容器左侧的距离了。

那其实 scrollLeft = 0 就等价于滚动条在最左边，scrollTop = 0 就等价于滚动条在最顶层。 scrollLeft 就是原有 api 参数中的 x，scrollTop 就是原有 api 参数中的 y。

## Reference

[https://www.w3.org/TR/cssom-view-1/#extension-to-the-element-interface](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)

[https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)

[https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollTop](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)

[https://developer.mozilla.org/en-US/docs/Web/API/Element/scrollLeft](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollTo)