---
title: "Object.defineProperty 的一点想法和解读"
datePublished: Mon Oct 15 2018 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqaj0wdg000009l20fxgfe86
slug: objectdefineproperty
tags: javascript

---

> 之前只能兼容 IE8，现在可以兼容到 IE11 了，可以在日常中使用 Object.defineProperty 了 看到有人用 Object.defineProperty 去实现了常量定义，觉得很有意思，在这分享一下。

`Object.defineProperty`是可以对对象本身进行设置的方法。除了常见的对对象进行数据劫持的用法之外， 最基本的四个属性是可以做到保证属性不被任何改动影响的。

创建一个空的对象。为了保证这个常量对象的纯粹，我们让这个对象的原型为 null

```jsx
let constant = Object.create(null);
```

为了保证这个常量下的任意 Key-value 值，在定义完之后，不可变动，我们可以采用 Object.defineProperty 对其赋值。

```jsx
Object.defineProperty(constant, "MY_CONSTANT", {
  enumerable: true, // 可以被枚举
  configurable: false, // 不可以删除和设置该属性
  writable: false, // 不可以重写
  value: "MY_CONSTANT_VALUE"
})
```

这里有 4 个值，

1. `enumerable`: 是否可以被`for...in`和`Object.keys()`枚举。
    
2. `configurable`: 是否可以删除该 key 值，同时控制 “能否再次对该 key 值进行`Object.defineProperty`”的能力。
    
3. `writable`: 是否可以对该 key 值对应的 value 值重新赋值。
    
4. `value`: value 值本身
    

关于 enumerbale, [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)上有很大篇幅的描述：

所以在对对象 constant 下的 MY\_CONSTANT 这个属性进行 Object.defineProperty 之后，任何操作都无法更改该 key-value 值，启到常量的作用。

这对于一般常量使用来说，可能是一种 overkill，但是设计思路值得一看。