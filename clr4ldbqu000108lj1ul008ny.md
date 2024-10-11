---
title: "Rust match in JavaScript"
datePublished: Mon Jan 08 2024 07:18:47 GMT+0000 (Coordinated Universal Time)
cuid: clr4ldbqu000108lj1ul008ny
slug: rust-match-in-javascript

---

## 前言

在日常编程过程中，经常能碰到非常复杂的流程问题。除了用if-else，switch-case解决问题外，只能使用一些比较常见的设计模式来优化流程。 例如pattern matching（模板映射）

## 模板映射

举个栗子 🌰

* 最基础的写法
    

```javascript
js复制代码const Main = () => {
	const condition = xxx; // 判断条件
	const Component = condition === '1' ? ComponentA
		: condition === '2' ? ComponentB
		: ComponentC

	return (
		<div>
			<Component/>
		</div>
	)
}
```

* 优化后
    

```javascript
const componentMapper = {
	"1": ComponentA,
	"2": ComponentB,
}
const Main = () => {
	const condition = xxx; // 判断条件	
	const component = componentMapper[condition] || ComponentC
	return (
		<div>
			<Component/>
		</div>
	)
}
```

是不是看上去好了些？但其实有更好的解法

## Rust的match语法

Rust中的`match`语法算是Rust中的一大杀器。虽说是类`switch` 的方式，但是在此之上，rust提供了相当灵活的匹配方式。

```rust
fn main() {
    let number = 13;
    // TODO ^ Try different values for `number`

    println!("Tell me about {}", number);
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // TODO ^ Try adding 13 to the list of prime values
        // Match an inclusive range
        13..=19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),
        // TODO ^ Try commenting out this catch-all arm
    }

    let boolean = true;
    // Match is an expression too
    let binary = match boolean {
        // The arms of a match must cover all the possible values
        false => 0,
        true => 1,
        // TODO ^ Try commenting out one of these arms
    };

    println!("{} -> {}", boolean, binary);
}
```

是不是看上去很完美～，如果在JavaScript中能有这种语法，可以在某些情况下极大的简化流程。

## JavaScript中的“match”

让我们假想一下JavaScript中的match语法。还是以组件映射为例。

```javascript
const Main = () => {
	const condition = xxx; // 判断条件	
	const component = match(condition, {
		"1": ComponentA,
		"2": ComponentB,
		_: ComponentC
	})
	return (
		<div>
			<Component/>
		</div>
	)
}
```

是不是比之前的例子看上去都要舒服很多？但是紧跟着的问题来：如何实现这样的match语法呢？

将上文中的match部分提取出来：

```javascript
match(condition, {
	"1": ComponentA,
	"2": ComponentB,
	_: ComponentC
})

// match(target, matchers)
```

> 为了方便解释，我们将第一个参数称之为target, 映射的mapper我们叫做matchers，每一个object的 Key-Value 组合称之为matcher。

从这一小段代码可以直接看出来一些问题

1. 如果我的key为boolean类型，可以做映射匹配吗？
    
2. 如果我的key为number类型，可以做映射匹配吗？
    
3. `_` 又该如何实现呢？
    

让我们来一一解决这些问题。

## 尝试实现JavaScript中的”match”

### 解决key的问题

首先，JavaScript中Object的key只能是`string`或者`Symbol`

若key为`number` 类型, 则会在`object`创建时转化为`string`。

```javascript
var a = {
  1: 'hello',
  2: 'world'
}
Object.keys(a) // ['1', '2']
```

所以，当在这种情况下，使用object类型作为matchers的基本结构就不在使用了。

那如果使用Array呢？

```javascript
match(condition, [
	["1", ComponentA],
	["2", ComponentB],
	[_, ComponentC],
])

// match(target, matchers)
```

当matcher中的映射对象为一个普通array中的元素，岂不是就可以使用任何的数据类型了。

那我们可以先写一个基础的TypeScript类型，来表达以上的API。

```typescript
type Matcher<T, R> = [identifier: T, matcherFn: () => R]

type Matchers<T, R> = Matcher<T, R>[]

export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => R
```

### 解决`_`通配符

在我们上面的设计中，`_` 就是一个Array中的普通元素，我们需要给用户提供这个`_` 通配变量。

这时候，Symbol就起到了作用。

> `Symbol` is a built-in object whose constructor returns a `symbol` primitive— also called a **Symbol value** or just a **Symbol** — that's guaranteed to be unique — [MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FSymbol)

Symbol在创建后一定唯一。如果使用Symbol作为我们的fallback标志符，岂不是正好。

那我们可以先创建一个Symbol，并导出。

```javascript
export const _ = Symbol("PLACEHOLDER");
```

同时，为了区别普通的matcher，我们也为`_` 所代表的fallback matcher，写一个TypeScript类型。

```typescript
export const _ = Symbol("PLACEHOLDER");

type Matcher<T, R> = [identifier: T, matcherFn: () => R];

type FallbackMatcher<R> = [identifier: typeof _, matcherFn: () => R];

type Matchers<T, R> = (Matcher<T, R> | FallbackMatcher<R>)[];

export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => R;
```

API的基础设计有了，fallback标志符也有了，接下来，让我们解决真正match的核心逻辑。

## 实现Match

其实整体的逻辑非常简单，检查每一个matcher中的identifier，若匹配，则直接调用，否则调用fallback matcher。

但在真正写逻辑之前，我们考虑一些特殊的case

1. 如果有多个相同的matcher，应该怎么处理。
    
2. 如果有多个相同的fallback matcher，应该怎么处理。
    
3. 如果没有fallback matcher，应该怎么处理？
    

作为一个api的实现者，需要在这里做一些取舍，但是我们有一个很好的参考，那就是Rust本身。

为了方便不熟悉Rust的同学了解match，我直接给一些结论。

在Rust中的match要求满足

1. 所有的matchers必须覆盖target的全部可能
    
2. 若有相同的matchers，则以第一个为准。
    

> 但是相比于JavaScript，Rust是可以提前做预检的，这也是静态类型语言的优点。
> 
> 在Rust中，若所有的matchers没有覆盖所有的case，是可以在编译前检测出来的，而JavaScript做不到
> 
> 在JavaScript里，我们只能舍弃这个可能性，转而让用户在调用时必须提供fallback matcher。

整体逻辑如下：

1. 检查是否有fallback matcher，若不存在，直接报错。
    
2. 提取出所有的非fallback matcher（按顺序），从头至尾依次匹配。
    
3. 若matcher匹配到了，则直接返回，终止后续匹配逻辑。
    
4. 若所有matcher都未匹配到，执行fallback matcher的逻辑。
    

让我们开始逐步实现。

### 第一步: 区分Matcher

我们要将matcher的两个部分分开，即fallback matcher + 其余matcher。也就是将一个array的matcher，分成2个部分。

我们可以使用2次filter进行分组。但是我们提供的`match` 在预期内应该是一个相当高频的函数调用，2次filter可能带来潜在性能问题。

我们来手写一个partition方法。

> 要注意，我们不能修改原有的数组arr，所以创建2个arr来暂存我们2个结果数组。

```typescript
/**
 * partition an Array into two.
 *
 * returns a Tuple which contain the desired elements and the rests
 *
 * @param arr the original array
 * @param partitionFn a function that pick the desired elements
 * @returns
 */
export const partition = <T>(
  arr: Array<T>,
  partitionFn: (element: T) => boolean,
): [Array<T>, Array<T>] => {
  const length = arr.length
  const target: Array<T> = []
  const rest: Array<T> = []

  for (let i = 0; i < length; i++) {
    const curr = arr[i]

    if (Boolean(partitionFn(curr))) {
      target.push(curr)
    } else {
      rest.push(curr)
    }
  }

  return [target, rest]
}
```

> 以上partition方法，我也抽出了一个单独的NPM包， [**@citrus327/array-partition**](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fcitrus327%2Farray-partition)

`partition`有了，那我们开始实现。

```typescript
export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => {
  const [fallback, rest] = partition(matchers, (matcher) => {
    const [identifier] = matcher
    return identifier === _
  }) as [[] | [FallbackMatcher<R>], Matcher<T, R>[]]

	//.....
}
```

### 第二步：预检并抛出异常

```typescript
export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => {
  const [fallback, rest] = partition(matchers, (matcher) => {
    const [identifier] = matcher
    return identifier === _
  }) as [[] | [FallbackMatcher<R>], Matcher<T, R>[]]

  if (fallback.length < 1) {
    throw new Error("[match] should contain at least one fallback matcher")

	// .....

}
```

当fallback matcher的长度不足1，我们需要抛出异常。

这样我们后续就不在考虑这些特殊case了。这也是exit-first编程思想的体现。

### 第三步：当matcher映射到，直接返回。

```typescript
export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => {
  // .....

  let matcherFound = false
  const length = rest.length
  let result = undefined

  for (let i = 0; i < length; i++) {
    const curr = rest[i]
    const [identifier, fn] = curr
    if (identifier === target) {
      matcherFound = true
      result = fn()
      break
    }
  }

  if (matcherFound) {
    return result
  }

	// .....
}
```

当`identifier === target` 时，我们将`matcherFound`标记置为true，并执行函数，然后跳出循环，返回值。在这种情况下，我们若存在多个相同的matcher，也只会匹配到第一个。

### 第四步：若没有匹配到，返回fallback matcher的执行结果。

```typescript
export const match = <T = any, R = any>(
  target: T,
  matchers: Matchers<T, R>,
) => {
	// ....

  if (fallback.length !== 0) {
    const [, fallbackFn] = fallback[0]
    return fallbackFn()
  }
}
```

同样，我们需要考虑若存在多个相同的fallback matcher，我们直接取第一个fallback matcher进行函数执行。

若没有fallback matcher，我们的函数将直接跳出，即返回undefined。

以上就是match的全部实现了。

## 剩下的问题

1. 其实我们只是实现了一个相当基本的match方法，Rust中的match可映射的case可太多了。我们如何做到覆盖更多的调用方式呢？
    
    ```rust
    match number {
        // Match a single value
        1 => println!("One!"),
        // Match several values
        2 | 3 | 5 | 7 | 11 => println!("This is a prime"),
        // TODO ^ Try adding 13 to the list of prime values
        // Match an inclusive range
        13..=19 => println!("A teen"),
        // Handle the rest of cases
        _ => println!("Ain't special"),
        // TODO ^ Try commenting out this catch-all arm
    }
    ```
    
    * `2 | 3 | 5 | 7 | 11` ：即匹配任意表明的值
        
    * `13..=19`：匹配13-19之间的值
        
2. 目前代码中，`identifier`与`target`的判断，是通过`===` 来执行的，我们是否可以抽象一个`equalityFn`来提供更多的幂等判断？
    
3. 在我们的API中要求必须包含一个fallback matcher是否在某些真不需要fallback的时候，而显得过于累赘？
    

## 写在最后

为了方便大家使用，文中的代码已经开源至：[github.com/citrus327/m…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FSymbol)

可以直接安装使用哦。

这也是我2024年的第一个轮子～ 欢迎大家star，分享，也欢迎大家来共建～