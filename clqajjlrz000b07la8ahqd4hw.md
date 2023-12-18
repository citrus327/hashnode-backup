---
title: "useSelector 导致的无意义 re-render 问题记录"
datePublished: Wed Jun 23 2021 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajjlrz000b07la8ahqd4hw
slug: useselector-re-render
tags: javascript, reactjs

---

## 简介

简述最近碰到的一次 re-render 问题，解决思路，以及后续的解决方案。

## 问题

某些组件在不应该 re-render 的情况下，n 次 re-render

组件具备以下特性

* 无 props
    
* 数据均订阅至 redux store
    

## 思路描述

一个组件被更新并 re-render，无非

* 父组件更新导致子组件 re-render
    
* 组件的 local state 更新
    
* 组件的 props 更新
    
* 组件订阅的 global state 更新，触发组件 re-render
    

经查证父组件、local state 等，只有第四条符合组件被 re-render 的可能性。组件内均通过 useSelector 进行 global state 的绑定（订阅）

## 定位问题

### useSelector 文档

通过重新查看 redux useSelector 的[api 文档](https://react-redux.js.org/api/hooks#equality-comparisons-and-updates)发现。

> With useSelector(), returning a new object every time will always force a re-render by default.

当 useSelector 返回一个新的对象，会强制 re-render。

一个 store 对象在任意 action 被 dispatch 之后，根据 flux 的规范，需要返回一个完整的 store 对象。

这句话等同于：store 内容本身的指针在每次 dispatch 后会更新为新的指针，除了 dispatch 的 actions 相关的 key 会变更，其他的 state 均为 merge 操作（不考虑 primitive 类型，仅考虑引用，因为 primitive 在进行 strict equal 时较为简单明了）

使用一个简单的 reducer 举例

例如：

```jsx
const setObj = (
  state,
  payload,,
) => ({
  ...state,
  obj: payload,
})
```

以上 setObj action 被 dispatch 后，会设置一个新的 obj，及为一个新的指针

使用

```jsx
const obj = useSelector((store) => store.obj);
```

[当执行`dispatch.store`](https://react-redux.js.org/api/hooks#equality-comparisons-and-updates)`.setObj(data)`后, obj 的指针本身会变化。达到了订阅的目的。

到目前为止都没有问题，结合上文说到的

> With useSelector(), returning a new object every time will always force a re-render by default.

obj 变更时，obj 确实为一个新的对象，会触发一次 re-render

### 多个 useSelector 的使用

假设 store 内有 2 个 obj，且某个组件对这 2 个 obj 均进行订阅

例：

```jsx
const TestComponent = () => {
  const { objA, ObjB } = useSelector((store) => {
    return {
      objA: store.objA,
      objB: store.objB,
    };
  });
  // ...
};
```

如果使用上文写法，会造成无意义的 re-render 问题

再次阅读 redux 关于 useSelector 的文档

> With useSelector(), returning a new object every time will always force a re-render by default.

当 useSelector 被执行后，这个钩子会比对此次 selector 调用与上次 selector 调用的结果进行比对，并进行===的 strict equal，参考文档：

> However, when an action is dispatched to the Redux store, useSelector() only forces a re-render if the selector result appears to be different than the last result. As of v7.1.0-alpha.5, the default comparison is a strict === reference comparison. This is different than connect(), which uses shallow equality checks on the results of mapState calls to determine if re-rendering is needed. This has several implications on how you should use useSelector().

回到例子

```jsx
const TestComponent = () => {
  const { objA, ObjB } = useSelector((store) => {
    // 这个selector每次调用都返回新的对象
    return {
      objA: store.objA,
      objB: store.objB,
    };
  });
  // ...
};
```

这也就符合了文档上描述的 always force a re-render by default

也就是说当前 store 作用域内（Provider 内），任意一个 action 被 dispatch，例子中的 selector 被执行后每次都会返回一个新的对象，并会强行触发一个 re-render，引发性能问题。

## 关于 useSelector 的正确使用？？？

### 方式一

```jsx
const TestComponent = () => {
  const { objA, ObjB } = useSelector((store) => {
    return {
      objA: store.objA,
      objB: store.objB,
    };
  });
  // ...
};
```

结论：**不可用**，每次都返回一个 new object，从而导致 re-render。

### 方式二：

```jsx
const TestComponent = () => {
  const objA = useSelector((store) => store.objA);
  const objB = useSelector((store) => store.objB);

  // ...
};
```

结论：**可用**，确实可以解决问题，组件按照预期渲染。useSelector 会被调用多次，但是得益于 batch patching，不会有性能问题。

### 方式三：

```jsx
import { shallowEqual } from "react-redux";
const TestComponent = () => {
  const { objA, ObjB } = useSelector((store) => {
    return {
      objA: store.objA,
      objB: store.objB,
    };
  }, shallowEqual);
  // ...
};
```

结论：**可用**，因为 shallowEqual 可以保证此次与上次的返回值的比对。告知 useSelector 这并不是一个新的对象，可以复用原值。从而不会导致 re-render。缺点在于：shallowEqual 会带来额外的计算损耗。

### 结论四

使用 memorized selector 进行选择器缓存。原理跟方式三同理。没有具体实验过，不过赘述。redux 文档有比较具体的[说明](https://react-redux.js.org/api/hooks#using-memoizing-selectors)。

## 总结

目前看来 useSelector 的使用总有些变扭，无论是单独写 selector 的冗余，还是多个 selector 要使用 shallowEqual 的额外参数，还是引入 reselect 做 memorized selector，都有额外的理解和开发成本。