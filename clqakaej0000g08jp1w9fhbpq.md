---
title: "Rust学习记录"
datePublished: Tue May 16 2023 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqakaej0000g08jp1w9fhbpq
slug: rust
tags: rust

---

## 简介

Rust学习的再再次尝试。所有coding相关会记录在[https://github.com/citrus327/give-rust-one-more-chance](https://www.flenker.blog/hecto-chapter-1/)

遇到的问题，或者需要记录的知识点将会在文中体现（不重要或已知的忽略不计）。会尽量按照自己对问题的理解进行类目分析

最终目标：

* <s>Follow </s> [<s>https://www.flenker.blog/hecto-chapter-1/</s>](https://www.flenker.blog/hecto-chapter-1/) <s> 完成一个编辑器</s>
    
    * <s>最后做到了chapter 4, 继续完成意义不大。</s>
        

1. 使用Rust完成一个简易CLI，可以进行一定的流程流转，最终写入文件或修改文件
    
2. 使用Rust调用swc等以Rust为基础语言的基建工具，做二次开发
    
3. 参考 [https://github.com/MunGell/awesome-for-beginners](https://www.flenker.blog/hecto-chapter-1/) Rust部分
    
4. 参考[https://github.com/codecrafters-io/build-your-own-x](https://www.flenker.blog/hecto-chapter-1/) Rust部分
    
5. [https://the-ray-tracing-road-to-rust.vercel.app/](https://www.flenker.blog/hecto-chapter-1/)
    
6. [https://tomassedovic.github.io/roguelike-tutorial/](https://www.flenker.blog/hecto-chapter-1/)
    

## Types

* const定义的常量类型必须定于类型。
    
    * const可以定义表达式，但是表达式中必须全部为常量。以下例子将报错
        
    
    ```rust
    const A_CONSTANT_VALUE: i32 = 6;
    let tmp: i32 = 5;
    // "tmp" attempt to use a non-constant value in a constant
    const YET_ANTHOER_CONSTANT_VALUE: i32 = A_CONSTANT_VALUE * tmp;
    ```
    
* Shadowing: 覆盖定义
    
    * 本质与mut不同，类似reassign一个值，只是覆盖了名称而已。
        
    
    ```rust
    let x = 5;
    let x = 6;
    // 允许的语法，x为6
    ```
    
* Rust属于静态类型语言，也就是说所有类型需要在compile时全部已知，有些API，类似于string.parse会要求主动声明parse完成的类型。
    
    ```rust
    // u32不加的话，会报出compile error, 因为rust无法推断guess的类型
    let guess: u32 = "42".parse().expect("Not a number!");
    ```
    

### Scalar Types (Primitive types)

* Integer
    
    * 类型的范围：
        
    
    | Length | Signed | Unsigned |
    | --- | --- | --- |
    | 8-bit | i8 | u8 |
    | 16-bit | i16 | u16 |
    | 32-bit | i32 | u32 |
    | 64-bit | i64 | u64 |
    | 128-bit | i128 | u128 |
    | arch | isize | usize |
    
    * signed的范围
        
        $$ \[ -(2^{n - 1}) , 2^{n - 1 }- 1\] $$
        
    * unsigned的范围
        
        $$ \[ 0, 2^{n}- 1\] $$
        
    * i8的范围即为-128到127，u8即为0到255
        
    * Integer overflow在rust中的解决方案？？
        

### Compound Types

* Tuple
    
    * Rust中的Tuple解构
        
        ```rust
        fn main1() {
            let tup = (500, 6.4, 1);
        
            let (x, y, z) = tup;
        
            println!("The value of y is: {y}");
        }
        
        fn main2() {
            let x: (i32, f64, u8) = (500, 6.4, 1);
        
            let five_hundred = x.0;
        
            let six_point_four = x.1;
        
            let one = x.2;
        }
        ```
        
* Array
    
    * Array内的所有元素必须为统一类型
        
    * Array的类型定义：`let a: [i32; 5] = [1, 2, 3, 4, 5];`
        

## Functions

* Functions不存在js中的变量提升，在Rust中只要被定义的函数就可以被定义，因为Rust在编译过程中会进行函数声明信息的收集
    
* 注意`x + 1`和`x + 1;`的区别，一个是expression, 一个是statement。
    

```jsx
fn main() {
		// {..}代表一个block
    let y = {
        let x = 3;
        x + 1 // 没有分号，代表一个expression，即当前block返回4，同时赋值给y
    };

    println!("The value of y is: {y}");
}
```

* function的类型定义：
    

```rust
fn five() -> i32 { // 使用->定义返回类型。
    5
}
```

* function可以使用return关键字来提供当前函数的返回值，或者缺省分号，使用最后一行expression的值作为返回值。
    

## Control Flows

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }

    // 可以直接赋值
    let number = if true { 5 } else { 6 };
    println!("The number is {}", number);

    // infinite loop
    // loop {
    //     println!("again");
    // }

    // 使用loop + break直接使用循环对一个变量赋值
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };
    println!("Result is {result}");

    // while loop
    let mut count: i32 = 5;

    while count != 1 {
        println!("in while loop");
        count -= 1;
    }

    // for loop an array
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

## Ownership

* 前言：Heap and Stack
    
    * Stack存放已知且固定大小的数据。Heap存放未知大小的数据，但会返回指针，指针本身为已知且固定的数据，可存入Stack中。
        
    * 存放数据：stack的速度大于heap，因为mem allocator需要找寻一个足够大的的地方存够数据且为下次存放做准备。
        
    * 获取数据：stack的速度大于heap，因为需要follow一个指针进行查找，而且处理器更擅长寻找相互临近的数据（stack数据相对临近）
        
    * 调用函数的场景：parameter and local variables入栈，函数结束后全部出栈
        
    * ownership解决的问题：Keeping track of what parts of code are using what data on the heap, minimizing the amount of duplicate data on the heap, and cleaning up unused data on the heap so you don’t run out of space are all problems that ownership addresses
        
* 规则：
    
    * Each value in Rust has an *owner*. - 每个值均有一个owner对应
        
    * There can only be one owner at a time. - 当值转移的时候，该值的owner会转移（move）到另外一个owner
        
    * When the owner goes out of scope, the value will be dropped. - 出scope后直接gc了
        
* 详细解释：
    
    下方图片展示的是shallow copy一个heap中的string类型，即s2会复制s1的指针，s1和s2会同时指向一个内存空间。但是这个会带来一个问题就是当s1 s2均跳出scope后，Rust会free2个指针，导致double free问题。所以这并不是Rust做的 ❌
    
    ![](https://file.notion.so/f/f/11f66c5f-6043-406b-b492-4dc267645668/fc983284-0f85-4003-bba4-35281418a791/Untitled.png?id=18599614-7b35-4298-b5b0-c5fe2d7e08f5&table=block&spaceId=11f66c5f-6043-406b-b492-4dc267645668&expirationTimestamp=1702972800000&signature=ZKG2JlaEvmYbsSpLaYO-Ye1l8YhYBdiv6giFuPHyE58&downloadName=Untitled.png align="left")
    
* 相对的，Rust为了解决这种问题，没有采取2个指针指向一个内存空间的解法。而是采用ownership的机制，见下图 ✅
    
* ![](https://file.notion.so/f/f/11f66c5f-6043-406b-b492-4dc267645668/176f2dc9-d1a8-4f57-9a20-810d52877919/Untitled.png?id=37d1fa51-4d97-4814-b87f-53b18a03a07e&table=block&spaceId=11f66c5f-6043-406b-b492-4dc267645668&expirationTimestamp=1702972800000&signature=k4UjKog-CvUQs6uUxaL2CtZ8WRW9kkUdKiG-T10Vh_M&downloadName=Untitled.png align="left")
    
    当`s2 = s1` 后，s1的owner会转移到s2，也就是说执行完成后s2为这块内存的owner，s1直接被gc。所以在这行代码执行完成后s1不再存在。但是可以clone（类似deep clone）来复制这片内存空间
    
    ```rust
    let s1 = String::from("hello");
    // s1已经move到了s2, s1在后续scope中不再存在
    // let s2 = s1;
    // 可以使用clone来进行shallowcopy
    let s2 = s1.clone();
    
    println!("{}, world!", s1);
    ```
    
    以上所有说的均是针对heap中的数据类型（即String)
    
    ---
    
    而针对stack中存放的数据（初始类型）由于本身存放在stack中，操作复制的成本很低，所以不存在shallow copy与deep copy一说（因为实际效果一致）
    
    针对stack中的数据，Rust可以添加一个叫做`Copy`的Trait到对应类型中，带有Copy Trait的类型可以保证值在assign后继续时候（被copy)。相对应的，在内存中的数据类型（例如String）会带有一个叫做`Drop`的Trait，实现`Drop`的Trait不能实现`Copy`。
    
    而实现了`Copy` trait的类型一共5种：即4个初始类型（scalar types)还有仅含有初始类型的tuple，例如`(i32, i32)` ，而`(i32, String)`则不是，因为String不是初始类型
    
* 函数中的ownership
    
    当值传入函数中，相当于ownership的转移，函数的返回值的ownership会转交到外部。
    
    以下的例子中由于s1的ownership转换，s1不再是s2，s1无法被重用。
    
    所以在函数中来回传递heap中的值，会较为麻烦（因为ownership一直转移），所以Rust含有Reference和Borrowing的概念来解决这个麻烦。
    
    ```rust
    fn main() {
        let s1 = String::from("hello");
    
        let (s2, len) = calculate_length(s1);
    
    		// 由于s1的ownership转换，s1不再是s2，s1无法被重用
        println!("The length of '{}' is {}.", s2, len);
    }
    
    fn calculate_length(s: String) -> (String, usize) {
        let length = s.len(); // len() returns the length of a String
    
        (s, length)
    }
    ```
    

### Reference & Borrowing

`&` 符号代表着取对应指针的引用。

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}. The Ref is {}", s1, len, &s1);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

以上代码的`s1` `s`的关系可以用下图说明, 即`s`就是一个新的指针指向`s1`

![](https://file.notion.so/f/f/11f66c5f-6043-406b-b492-4dc267645668/a7c72a6a-975c-4a0b-8ff3-90111c829e5b/Untitled.png?id=d6c2e8d9-cf35-4548-a7cd-1621eac6942f&table=block&spaceId=11f66c5f-6043-406b-b492-4dc267645668&expirationTimestamp=1702972800000&signature=hG4T8ehPkwFcDST3t_CjK0B1grogDrF4eOz7zMpOP20&downloadName=Untitled.png align="left")

`&s1`在传入函数后，可以继续使用的原因是`&s1`是`s1`的一个引用，但是因为他不是对应值的owner，所以不会drop。且函数不需要返回来交出ownership，因为引用压根不存在ownership一说。

当创建了一个reference之后，这种行为就叫做borrowing,因为本质上 reference并不存在ownership，即为借用。

### Mutable Reference

一个普通的reference是不能够被修改的，相对可以使用`&mut` 来创建一个可修改的引用。

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);

		// The changed s is hello world
    println!("The changed s is {s}");
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

---

说一下mutable reference的一些特点和限制：

* mutable reference的限制是只能存在一个mutable的reference。这种设计是为了防治data race，即竞争关系。
    
    当然可以通过创建一个scope来完成2个&mut的存在。
    

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);

    println!("The changed s is {s}");

    {
        let r1 = &mut s;
        r1.push_str(" inner")
    } // r1 goes out of scope here, so we can make a new reference with no problems.

    let r2 = &mut s;

    r2.push_str(" outter");

		// after two &mut, s is $hello, world inner outter
    println!("after two &mut, s is ${s}")
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

只读的reference可以多次存在，因为每个引用只能进行读取操作。

* 当mutable reference和reference同时存在时，已经被借用的值，不能被借用为mutable reference。 换一种理解，就是reference和mutable reference的scope不能重叠。一个reference的scope，是从声明到最后一次使用。在println!之前，r3与r1 r2同时出现。reference和mutable reference的scope重叠就会导致compiler error。见下面的2个例子。
    

```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
let r3 = &mut s; // BIG PROBLEM

println!("{}, {}, and {}", r1, r2, r3);
```

下面的例子依旧有问题

```rust
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
println!("{}, {}", r1, r2);

let r3 = &mut s;
r3.push_str(" world");

// r1, r2的scope在这结束，而在这中间，r3这个mut reference的scope相互重叠了。
println!("{}, {}, {}", r1, r2, r3);
// println!("{}", r3); // 使用这行将解决这个问题
```

### Dangling Reference

尝试返回一个已经被drop的值的引用，会造成dangling reference, 但是Rust compiler会给出编译报错。下文的例子就是一个dangling reference。

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

		// s is dropped after dangle fn(), but we try to return a reference value to it.
    &s
}
```

## Structs

Struct（类似JavaScript中的object)

* 对整体struct进行修改时，需要对struct实例整体表明`mut`
    
* merge使用`..` 符号，merge的顺序与js相反。且`..`的merge必须放在最后一行。
    
* 整体规则符合ownership，heap variable和stack variable的规则同样受用。
    

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}

fn build_user(email: String, username: String) -> User {
    User {
        active: true,
        username: username,
        email: email,
        sign_in_count: 1,
    }
}

fn main() {
    let mut user1 = User {
        active: true,
        username: String::from("someusername123"),
        email: String::from("someone@example.com"),
        sign_in_count: 1,
    };

    // user1需要整体标注为mut
    user1.email = String::from("anotheremail@example.com");

    let user2 = build_user(
        String::from("citrus327@outlook.com"),
        String::from("hophop")
    );

    // 取user1的username，剩下的采用user2，而非使用user2进行全量覆盖
    let merged_user = User {username: user1.username, sign_in_count: user2.sign_in_count, ..user2 };

    println!("user email is {}", user1.email);

    // user1.username已经被merged_user借用，无法再次使用。
    // println!("user email is {}", user1.username); 

    // 而sign_in_count的类型属于u64, 属于scalar type, 可以再次使用（实现了Copy Trait)
    println!("user2 sign_in_count is {}", user2.sign_in_count); 
    println!("merged user name is {}", merged_user.username); // someusername123
    println!("merged user email is {}", merged_user.email); // citrus327@outlook.com
}
```

* struct可以定义方法，属于struct本身（或者与struct本身有关联的）称之为method。与struct本身无关的，称之为associated functions。associated functions可以使用`Rectangle::print()`来调用。
    
    * P.S. associated functions类似于直接调用类下的静态方法，method即为成员方法。但是每个成员方法也可以使用associated functions的方式来调用。或者说表明了&self的，可以直接使用实例调用（当然也可以静态调用）这点Rust做的相当灵活，类似于js的`apply`
        

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

		// method
    fn print(&self) {
        println!("The area of the rectangle {:#?} is {}", self, self.area());
    }
		// associated functions
    fn square(size: u32) -> Self {
        Self {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let rectangle = Rectangle {
        width: 40,
        height: 40
    };

    // println!("The area of the rectangle {:#?} is {}", rectangle, calc_area(&rectangle));
    // println!("The area of the rectangle {:#?} is {}", rectangle, rectangle.area());
    rectangle.print();
		// 可以使用这种方式（associated functions）调用。
		Rectangle::print(&rectangle);

		// associated functions的调用方式
    let square = Rectangle::square(12);

    square.print();

}

fn calc_area (rectangle: &Rectangle) -> u32{
    return rectangle.width * rectangle.height;
}
```

## Enums

枚举，除了是个枚举之外，Rust中的枚举一定程度可以担当structs的部分工作。

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

这段代码中的`Message`枚举拥有4种值，且每种值可以hold不同类型的数据。

---

除了以上功能外，一个比较重要的枚举是`Option` ，`Option`枚举拥有2个值，`None`和`Some`，`None`代表不存在，`Some`代表值存在。`None`和`Some`可直接在代码中引用。或使用通用的枚举synxtax，例如`Option::None`

Option的主要目的是为了解决Null的问题。关于Null可能引发的问题：

> the concept that null is trying to express is still a useful one: a null is a value that is currently invalid or absent for some reason.

解决方案：

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;

```

当`y`可能为`null`时，使用`Some`来代表`y`这个值能不存在，当`y`被引用时，一个`i8`是无法与一个`Option<i8>`类型相加的，所以需要一定的方式将`Some<T>`转换为`T`，进而进行使用。

## Pattern Matching

基础写法

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

每个enum在被match时，每个variant都需要被覆盖到。

可以使用`other`关键字或者`_`关键字进行额外分支的处理。

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        // Coin::Nickel => 5,
        // Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("the state is from {:?}", state);
            return 25;
        }
				// 使用other做缺省处理，例如js中switch case的default
        other => {
            println!("other");
            return 2;
        }
    }
}

fn main() {
    println!("value is {}", value_in_cents(Coin::Nickel));
}
```

与JavaScript的switch case一致，other需要放在最后。

`_`符号指不需要对额外分支做处理。

---

可以使用`if let`简化pattern matching的冗余代码

```rust
let config_max = Some(3);
match config_max {
    Some(max) => println!("The maximum is configured to be {}", max),
    _ => (),
}
```

当`config_max`是一个为3的`Some`，当`Some`通过`match`检测后，打印出`max`的值，否则不做任何事情。在这种case下`_`的分支就会冗余。

通过`if let` 可以简化以上代码为：

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {}", max);
}
```

`=`左侧的为需要match的条件，`=`右侧为被match的变量，对应的大括号内的为首个映射上的分支处理代码。

### unwrap 系列

绝大多数情况使用unwrap\_or\_else，既可以处理错误不让程序 crash，也可以拿到返回值。

如果不关心错误存在，可以使用`unwrap_or`

如果期待有问题出现，使用unwrap\_err。

* `unwrap()`
    
    * **Panic when error occurs**
        
    * This function is used to extract the value from an `Option` or `Result` type, assuming that it contains a value. If the `Option` or `Result` is `Some(value)` or `Ok(value)`, `unwrap()` returns the inner value. However, if the `Option` or `Result` is `None` or `Err`, `unwrap()` will panic and crash the program.
        
* `unwrap_err`
    
    * **Panic when no error occurs**
        
    * This function is specific to `Result` types. It is the counterpart of `unwrap()` for handling `Err` variants. If the `Result` is `Ok(value)`, `unwrap_err()` will panic and crash the program. But if the `Result` is `Err(error)`, it returns the inner error value.
        
    * Example:
        
        ```rust
        fn divide(a: i32, b: i32) -> Result<i32, String> {
            if b == 0 {
                Err(String::from("Cannot divide by zero"))
            } else {
                Ok(a / b)
            }
        }
        
        fn main() {
            let result = divide(10, 0);
            let err_message = result.unwrap_err();
            println!("Error occurred: {}", err_message);
        }
        ```
        
* `unwrap_or(default)`
    
    * **Won’t panic**
        
    * This function is used to get the inner value from an `Option` or `Result`, or provide a default value if it is `None` or `Err`. If the `Option` or `Result` is `Some(value)` or `Ok(value)`, `unwrap_or()` returns the inner value. But if the `Option` or `Result` is `None` or `Err`, it returns the provided `default` value.
        
* `unwrap_or_default()`
    
    * **Won’t panic**
        
    * This function is similar to `unwrap_or()`, but it returns the default value of the inner type if the `Option` or `Result` is `None` or `Err`. The default value is determined by the `Default` trait implemented for the inner type.
        
* `unwrap_or_else(default_fn)`
    
    * **whether it crashes or not completely depends on the closure function**
        
    * This function is also used to get the inner value from an `Option` or `Result`, or provide a fallback value or mechanism if it is `None` or `Err`. If the `Option` or `Result` is `Some(value)` or `Ok(value)`, `unwrap_or_else()` returns the inner value. But if the `Option` or `Result` is `None` or `Err`, it calls the provided `default_fn` closure or function to generate the fallback value.
        
    * Example
        
    
    ```rust
    fn divide(a: i32, b: i32) -> Result<i32, String> {
        if b == 0 {
            Err(String::from("Cannot divide by zero"))
        } else {
            Ok(a / b)
        }
    }
    
    fn main() {
        let result = divide(10, 2);
        let value = result.unwrap_or_else(|err| {
            println!("Error occurred: {}", err);
            0
        });
        println!("Result: {}", value);
    }
    ```
    

## Vector

* 创建Vector的方式：
    

```rust
// 从0创建
let mut v: Vec<i32> = Vec::new();

// mutating vector
v.push(1);
v.push(2);
v.push(3);
v.push(4);

// 包含初始值
let v = vec![1, 2, 3, 4, 5];
```

* 修改Vector的方法：`push`, `pop`等, 不过多赘述
    
* 遍历与读取：
    

```rust
fn iterate_vec(v: &Vec<i32>) {
    for i in v {
        println!("the value at index {i} is {}", i);
    }
}

fn reading_vec_by_index(v: &Vec<i32>, index: usize) {
    println!(
        "reading index {} from vector {:?}, value is {}",
        index, v, v[index]
    );
}

fn reading_vec_by_get(v: &Vec<i32>, index: usize) {
    // let third3 = v.get(index);

    // let result: i32;
    // match third3 {
    //     Some(v) => result = *v,
    //     None => println!("There is no third element."),
    // }

    // return result;

		// 使用.get去获取值，将会返回一个Option
    let third4 = v.get(index);
    if let Some(third) = third4 {
        println!(
            "reading index {} from vector {:?}, value is {}",
            index, v, third
        );
    } else {
        println!("There is no third element.");
    }
}
```

---

* 涉及到ownership相关：
    

当其中某个值被引用（借用），整体vector都会被hold。

当vector自身out of scope，整体vector都会被free

## Strings

String的创建，读取，修改，直接参考代码

```rust
fn main() {
    println!("Hello, world!");

    let str = create_str_from_new();
    print_str(&str);

    let str = create_str_with_initial_values();
    print_str(&str);

    let mut str = create_str_from_string_literal();
    print_str(&str);

    str.push_str(" world"); // can push str
    str.push('!'); // can only push char

    print_str(&str);

    // use + operator to append
    let str_part_1 = String::from("hello");
    let str_part_2 = String::from("world");

    // 需要使用&进行引用的原因是因为 + operator本质是调用add function，add fn的定义要求传入&str
    // add fn: fn add(self, s: &str) -> String {
    // 因为str_part_1的ownership被add拿走，str_part_1在+号后不再存在，而str_part_2为引用，后续依旧可用。
    let str = str_part_1 + " " + &str_part_2 + "!";

    // print_str(&str_part_1); // 报错 ownership
    // print_str(&str_part_2); // OK

    print_str(&str);

    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let str = s1 + "-" + &s2 + "-" + &s3;

    print_str(&str);

    let s1 = String::from("tic");
    let s2 = String::from("tac");
    let s3 = String::from("toe");

    let str = format!("{s1}-{s2}-{s3}");
    print_str(&str);

    // string不支持index访问。
    // 原因是String的本质实现是存放着asciicode的vector: Vec<u8>，
    // 若允许indexing，则会返回相对应的number，为了保证意外的情况，Rust完全禁止了String indexing
    // let s1 = String::from("hello");
    // let h = s1[0]; // Compile Error
}

fn create_str_from_new() -> String {
    let mut s = String::new();
    s.push('h');
    s.push('e');
    s.push('l');
    s.push('l');
    s.push('o');
    s
}

fn create_str_with_initial_values() -> String {
    let s = String::from("hello");

    s
}

fn create_str_from_string_literal() -> String {
    let s = "hello";

    s.to_string()
}

fn print_str(str: &str) {
    println!("The str created is {}", str);
}
```

---

String本身不支持index读取

原因：

1. String本身实现是由`Vec<u8>` 实现，通过index访问将会返回对应的ascii code
    
2. 某些unicode字符本身占用2个bytes，例如`नमस्ते`，从bytes角度看，展示为`[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]`，若能够通过index访问，将会返回number，且无法明确对应的index值。
    
3. Rust认为通过index访问String，应该保证一致的`O(1)` 复杂度，但实际并不是如此，且无法保证
    

所以一刀切不允许使用index访问了。

---

String Slicing

```rust
let str = "Здравствуйте";

let s = &str[0..4];
// Зд本身占用4个bytes，0-4会得到Зд，而不是Здра
print_str(&s);
```

本身也不是很可靠的slicing方案，主要还是因为`Vec<u8>` 这个设计的问题。。能不用别用了。。

---

可通过转成char后遍历String

```rust
for c in "Зд".chars() {
    println!("{c}");
}
```

## HashMap

没什么太多可注意的，ownership的转移关注一下。

```rust
use std::collections::HashMap;

fn main() {
    let scores = create_hash_map();

    let score = scores.get("Blue").copied().unwrap_or(0);

    println!("score is {}", score);

    iterate_hash_map(&scores);

    let field_name = String::from("Favorite color");
    let field_value = String::from("Blue");

    let mut map = HashMap::new();
    map.insert(field_name, field_value);

    // map.insert(&field_name, &field_value);

    // ownership已经被转移到了map，后续无法再获取field_name和field_value
    // println!("field_name {}", field_name);
    // println!("field_value {}", field_value);

    update_hash_map();
}

fn create_hash_map() -> HashMap<String, i32> {
    let mut scores = HashMap::new();

    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Yellow"), 50);

    scores
}

fn iterate_hash_map(map: &HashMap<String, i32>) {
    for (key, value) in map {
        println!("{key}: {value}");
    }
}

fn update_hash_map() {
    let mut scores = HashMap::new();

    // overwriting
    scores.insert(String::from("Blue"), 10);
    scores.insert(String::from("Blue"), 25);

    // overwriting by checking if already has value
    scores.entry(String::from("Yellow")).or_insert(50);
    scores.entry(String::from("Blue")).or_insert(50);

    println!("{:?}", scores);
}
```

## Crates, Packages and Modules

* crate: 分成binary crate和library crate两种
    
    * binrary crate可以编译成一个executable
        
    * library crate提供功能，在程序之间共享
        
* package: bundle of crates, package内含有`Cargo.toml` 文件进行描述如何构建内含的crates
    
    * 一个package可以有n多个binary crate，但是至多只能有一个library crate
        
    * 使用`cargo new`创建package
        
    * `src/`[`main.rs`](https://www.flenker.blog/hecto-chapter-1/) 是Rust package中默认的crate root
        
* module: 模块，使用`::` 调用模块子方法（或枚举或`struct`），使用`pub` 进行模块公开，默认模块及模块方法全私有。
    

---

模块内部可使用`self`调用自身模块的方法，`super`调用父模块的方法。

```rust
pub mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    pub mod serving {
        fn take_order() {
            super::serving::take_payment();
        }

        fn serve_order() {}

        fn take_payment() {
            self::serve_order();
        }

        pub mod testing {
            fn test_fn() {
                super::serve_order();
            }
        }
    }
}
```

---

同名module下的成员，可以使用`as`进行别名

```rust
use std::fmt::Result;
use std::io::Result as IoResult;

fn function1() -> Result {
    // --snip--
}

fn function2() -> IoResult<()> {
    // --snip--
}
```

---

关于模块设计、管理，一些解法：

[https://mmapped.blog/posts/03-rust-packages-crates-modules.html](https://www.flenker.blog/hecto-chapter-1/)

---

关于模块引用的例子, 直接看注释就好。

* 文件结构:
    
    |- src
    
    |- [editor.rs](https://www.flenker.blog/hecto-chapter-1/)
    
    |- [main.rs](https://www.flenker.blog/hecto-chapter-1/)
    
    |- [terminal.rs](https://www.flenker.blog/hecto-chapter-1/)
    
* [main.rs](https://www.flenker.blog/hecto-chapter-1/)
    
    ```rust
    // (1)
    pub use terminal::Terminal;
    
    fn main() {
        // xxx
    }
    ```
    
* [editor.rs](https://www.flenker.blog/hecto-chapter-1/)
    
    ```rust
    // (2) 
    // 可以直接使用crate的Terminal，其实应该写成 use crate::terminal::Terminal
    // 但是因为main.rs内部使用了pub use terminal::Terminal，
    // 相当于将terminal下的Terminal暴露至了crate级别
    // 类似js中index.js全部re-export，所有子模块就可以直接从入口处统一导入
    use crate::Terminal;
    ```
    
* [terminal.rs](https://www.flenker.blog/hecto-chapter-1/)
    
    ```rust
    pub struct Size {
        pub width: u16,
        pub height: u16,
    }
    
    pub struct Terminal {
        size: Size,
    }
    
    impl Terminal {
        pub fn default() -> Result<Self, std::io::Error> {
            // xxx
        }
    
        pub fn get_size(&self) -> &Size {
            &self.size
        }
    }
    ```
    

## Error Handling

* 两类异常：可恢复与不可恢复类型
    
* 使用`panic!` 应对不可恢复类型异常
    
    ```rust
    fn main() {
        // a manual panic! call
        // panic!("plz panic!");
    
        let vec = [1, 2, 3];
    
        // this will break the program
        vec[99];
    }
    ```
    
    当程序因为panic而停止运行后，Rust会打印出报错信息 → unwind → 清除栈 → 退出。unwinding本身会从堆栈信息中挨个获取每个function call对应的数据做清理。这个行为本身相当消耗性能（原文使用a lot of work形容）。可以通过配置
    
    ```toml
    [profile.release]
    panic = 'abort'
    ```
    
    来使程序在release中碰到panic直接中止程序，而将清理工作交给操作系统处理。
    
    [Unrecoverable Errors with panic! - The Rust Programming Language](https://doc.rust-lang.org/book/ch09-01-unrecoverable-errors-with-panic.html#unwinding-the-stack-or-aborting-in-response-to-a-panic)
    
* 使用`Result`来应对可恢复类型异常
    
    ```rust
    fn try_open_file_with_specific_errors() {
        let greeting_file_result = File::open("hello.txt");
    
        let greeting_file = match greeting_file_result {
            Ok(file) => file,
            Err(error) => match error.kind() {
                // 如果是NotFound的Kind，直接生成文件。
                ErrorKind::NotFound => match File::create("hello.txt") {
                    Ok(fc) => fc,
                    Err(e) => panic!("Problem creating the file: {:?}", e),
                },
                other_error => {
                    panic!("Problem opening the file: {:?}", other_error);
                }
            },
        };
    
        println!("success!, {:#?}", greeting_file)
    }
    ```
    
    使用match方法会显得略显累赘，使用`Result`相关的方法可以简化代码，例如使用`unwrap`以及`expect`
    
    ```rust
    use std::fs::File;
    
    fn main() {
        let greeting_file = File::open("hello.txt").unwrap();
    
    		let greeting_file = File::open("hello.txt")
            .expect("hello.txt should be included in this project");
    }
    ```
    
    若`Result`结果是`Ok`，`unwrap`则会直接返回`Result`内的值，否则会调用`panic!`。使用`expect`可以填入自定义的panic信息，报错信息会更加语义化。推荐使用`expect`
    
    * 除了函数体内直接处理异常外，也可以将`Err`传递。
        
    
    ```rust
    use std::fs::File;
    use std::io::{self, Read};
    
    fn read_username_from_file() -> Result<String, io::Error> {
        let username_file_result = File::open("hello.txt");
    
        let mut username_file = match username_file_result {
            Ok(file) => file,
            Err(e) => return Err(e),
        };
    
        let mut username = String::new();
    
        match username_file.read_to_string(&mut username) {
            Ok(_) => Ok(username),
            Err(e) => Err(e),
        }
    }
    ```
    
    这段代码会在`Err`分支内直接将错误返回，注意函数返回类型。
    
    * 简化`Result`以及`match`
        
    
    ```rust
    use std::fs::File;
    use std::io::{self, Read};
    
    fn read_username_from_file() -> Result<String, io::Error> {
        let mut username_file = File::open("hello.txt")?;
        let mut username = String::new();
        username_file.read_to_string(&mut username)?;
        Ok(username)
    }
    ```
    
    在`Result`后标明`?` 代表若有错误，直接返回一个`Err`的`Result`至外部。
    
    注意带有在`Result`后带有`？`则表示会对外界传递`Err` ，返回类型一定要注意写。
    

## Generic & Trait

* 范型：
    
    * 与其他语言类似，compiler时可确认，不影响性能。compiler时会执行Monomorphization，将特定类型填充至范型坑位
        
* Trait：特性？其实就是接口。
    
    * 一个简单的范型定义
        
        ```rust
        trait Summary {
            fn summarize(&self) -> String;
        }
        ```
        
    * 2个不同的struct，实现同一个接口
        
        ```rust
        pub struct NewsArticle {
            pub headline: String,
            pub location: String,
            pub author: String,
            pub content: String,
        }
        
        impl Summary for NewsArticle {
            fn summarize(&self) -> String {
                format!("{}, by {} ({})", self.headline, self.author, self.location)
            }
        }
        
        pub struct Tweet {
            pub username: String,
            pub content: String,
            pub reply: bool,
            pub retweet: bool,
        }
        
        impl Summary for Tweet {
            fn summarize(&self) -> String {
                format!("{}: {}", self.username, self.content)
            }
        }
        ```
        
    * 接口可以有默认实现
        
        ```rust
        pub trait Summary {
            fn summarize(&self) -> String {
                String::from("(Read more...)")
            }
        }
        ```
        
    * 要求参数必须实现某些接口
        
        ```rust
        fn need_to_impl_summary<T: Summary>(item: &T) {
            item.summarize()
        }
        
        // 缩略版
        fn need_to_impl_summary_simple(item: &impl Summary) {
            item.summarize()
        }
        ```
        
    * 要求参数实现多个接口
        
        ```rust
        pub fn notify<T: Summary + Display>(item: &T) {}
        
        pub fn notify(item: &(impl Summary + Display)) {}
        ```
        
    * 当范型要求比较多，可读性不高时可使用`where` 关键字
        
        ```rust
        fn some_function<T, U>(t: &T, u: &U) -> i32
        where
            T: Display + Clone,
            U: Clone + Debug,
        {}
        ```
        
    * 要求返回值必须实现某些接口
        
        ```rust
        fn returns_summarizable() -> impl Summary {
            Tweet {
                username: String::from("horse_ebooks"),
                content: String::from(
                    "of course, as you probably already know, people",
                ),
                reply: false,
                retweet: false,
            }
        }
        ```
        
        如果返回值要求实现`Summary`接口，这里有一个隐形的限制就是返回类型必须得是一种。必能返回2种不同的`struct`，但是都实现了`Sumary`
        

## Lifetimes

生命周期这个设计的引入主要是为了保证引用在我们需要时的有效性。

> Lifetimes are another kind of generic that we’ve already been using. Rather than ensuring that a type has the behavior we want, lifetimes ensure that references are valid as long as we need them to be.

给出一段错误的代码

```rust
fn main() {
    let r;

    {
        let x = 5;
        r = &x;
    }

    println!("r: {}", r);
}
```

这里可以看出这里可能会存在`r`被赋值一个无效的指针`x`。

尝试编译，编译器会给出

```rust
$ cargo run
   Compiling chapter10 v0.1.0 (file:///projects/chapter10)
error[E0597]: `x` does not live long enough
 --> src/main.rs:6:13
  |
6 |         r = &x;
  |             ^^ borrowed value does not live long enough
7 |     }
  |     - `x` dropped here while still borrowed
8 |
9 |     println!("r: {}", r);
  |                       - borrow later used here

For more information about this error, try `rustc --explain E0597`.
error: could not compile `chapter10` due to previous error
```

跟预期一致，x在内部scope结束后，指针引用被drop，但是依旧被外界继续引用

Rust能够做到这种指针引用check的主要办法是使用了borrow checker

---

```rust
fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {}", r); //          |
}                         // ---------+
```

以上代码可以看到`r`的引用存活范围在`’a`，`x`的引用存活范围在`’b`

可以从上图看到`’a`是远大于`’b`的，也就是说`x`的存活时间没有活到`’a`结束。这就是borrow checker的原理。

```rust
fn main() {
    let x = 5;            // ----------+-- 'b
                          //           |
    let r = &x;           // --+-- 'a  |
                          //   |       |
    println!("r: {}", r); //   |       |
                          // --+       |
}                         // ----------+
```

修改为以上代码后，`x`变量可以跟随`r`一直到末尾。也就满足`x`的存活时间活到了`r`结束。borrow checker检查通过。

---

看下面的函数：

```rust
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

以上代码的返回值在编译时是无法确认x和y哪个引用被返回，且无法使用上问的scope概念来区分2者到底哪个被返回。这时候需要使用lifetime anotation来标明引用的lifetime。lifetime anotation仅做标记作用，不会修改变量的引用关系。

写法：

```rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

为上面的函数加上lifetime anotation

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

以上代码表明了返回值的lifetime与`x`和`y` 一致，即只要`x` `y`存在，返回值就存在。

---

Lifetime in Struct

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.').next().expect("Could not find a '.'");
    let i = ImportantExcerpt {
        part: first_sentence,
    };
}
```

---

Lifetime的省略：

在老的rust版本中(before 1.0 pre)，每个函数与method都需要表明lifetime anotation，随着时间和版本的变迁，很多时候lifetime是可以被提前anotate的，所以一些常见的pattern被写入Rust Compiler中，这些patterns或者rules就被称为**lifetime elision rules**

这就说到Compiler的Lifetime Rule。

我们将函数、method的参数lifetime anotation称之为input lifetimes, 针对返回值就叫做output lifetimes。

Lifetime Rule一共3条，1条针对input lifetimes, 1条针对output lifetimes，最后一条针对method中的&self引用

1. 编辑器会给每个参数reference分配一个lifetime anotation
    
    1. 例如`fn first_word(s: &str) -> &str` 将变成 `fn first_word<'a>(s: &'a str) -> &str`
        
2. 如果只有一个input lifetime parameter，那输出必定也是这个lifetime parameter
    
    1. 例如`fn first_word<'a>(s: &'a str) -> &str` 将变成`fn first_word<'a>(s: &'a str) -> &'a str`
        
3. method中&self的lifetime会直接赋给所有的output lifetime
    

---

常量的lifetime

```rust
let s: &'static str = "I have a static lifetime.";
```

## Closure

闭包（匿名函数）具备自动捕获引用的功能，自动borrow immutable reference, borrow mutable reference, 且自己拿走ownership。

### 类型推断

```rust
let example_closure = |x| x;

let s = example_closure(String::from("hello"));

// 报错，Rustc会根据第一次调用进行类型推断，第二次的类型与第一次不同
let n = example_closure(5);

```

### 值的自动捕获

```rust
let list = vec![1, 2, 3];
println!("Before defining closure: {:?}", list);

// 使用闭包就不报错
let only_borrows = || println!("From closure: {:?}", list);

fn only_borrows() {
		// list报错，can't capture dynamic environment in a fn item
    println!("From function: {:?}", list);
}

println!("Before calling closure: {:?}", list);
only_borrows();
println!("After calling closure: {:?}", list);
```

### 3种闭包类型

FnOnce Fn

## Macro

参考：[https://doc.rust-lang.org/rust-by-example/macros.html](https://www.flenker.blog/hecto-chapter-1/)

### 简介

宏用于在 rust中进行`metaprogramming`。

* 宏的优点（摘自官网）
    

1. Don't repeat yourself. There are many cases where you may need similar functionality in multiple places but with different types. Often, writing a macro is a useful way to avoid repeating code.
    
2. Domain-specific languages. Macros allow you to define special syntax for a specific purpose. (More on this later)
    
3. Variadic interfaces. Sometimes you want to define an interface that takes a variable number of arguments. An example is `println!` which could take any number of arguments, depending on the format string.
    

* Rust 的宏（macros）和普通函数有以下区别：
    
    1. 调用时机：宏是在编译期间进行展开，而函数是在运行时执行的。这意味着宏的展开发生在代码编译阶段，而函数的执行发生在程序运行阶段。
        
    2. 语法：宏使用 `macro_rules!` 关键字定义，可以在代码中匹配和转换模式。而函数使用 `fn` 关键字定义，并遵循 Rust 函数的语法规则。
        
    3. 参数和类型检查：宏可以接受任意数量和类型的参数，并且不进行类型检查。它们在展开时根据上下文进行解释和处理。相比之下，函数在编译前会进行类型检查，并且参数的数量和类型必须与函数签名一致。
        
    4. 代码生成：宏可以生成任意数量和类型的代码，并在展开时将其插入到源代码中。而函数的结果是在运行时通过执行函数体得出的，无法在编译期间生成额外的代码。
        
    5. 功能扩展：宏具有更大的灵活性，可以用于元编程和代码生成。宏可以根据需要生成重复的代码、生成不同的代码分支、进行模式匹配等。这使得宏在代码重用和构建领域特定语言（DSL）方面非常有用。函数则更适合用于封装可重用的代码块。
        
* 元编程(`metaprogramming`)
    
    * 元编程（Metaprogramming）是一种编程技术，它使得开发人员能够编写能够生成、操作和转换程序代码的代码。元编程的目的是通过编写代码来生成代码，通过程序生成程序。宏是 Rust 中的元编程机制，允许你在编译期间操作代码，并使用宏定义来生成、转换或扩展代码。
        
* Macro Expanding （宏的展开）
    
    * 宏的展开指的是在编译期间，将宏调用所在的代码替换为宏定义中所指定的代码。这个替换过程发生在代码的预处理阶段，称为宏展开。
        
    * 使用宏，你可以编写一些代码模板，然后通过宏来生成具体的代码。宏可以接受参数，并可以基于这些参数在编译期间进行逻辑处理，最终生成符合需求的代码。这种能力使得宏在减少重复代码、生成大量重复代码、实现领域特定语言（DSL）等方面非常有用。
        
    * 例如，Rust 中的 `println!` 宏就是一个常见的宏。在编写代码时，你可以使用 `println!("Hello, {}!", name)` 来打印带有变量 `name` 值的消息。在宏展开时，`println!` 宏会根据提供的参数生成对应的代码，以在运行时打印消息。这样，你可以在编写代码时使用宏来简化打印操作，而不必手动编写打印代码的重复代码。
        
    * 总结起来，宏的展开是指在编译期间将宏调用替换为宏定义中所指定的代码，而元编程是一种编程技术，通过编写代码来生成、操作和转换程序代码，而不仅仅是在运行时执行代码。它使得宏能够在编译期间进行代码生成和转换，以提供更高的灵活性和复用性。
        

## Concurrency

Rust并没有在Concurrent（并发）和Parallel（并行）上进行很大程度的区分，而是希望通过一种解法，解决两者带来的问题。

> Parallel要求有多个可处理的CPU核心，而Concurrent要求多线程。如果没有多核CPU来承载多个任务执行，就不能以Parallel来解决问题。

* 关于M:N和1:1
    

有些语言在线程的处理上，会使用M:N的模型，这个的意思是程序创建M的“线程”，但是实际创建的线程是N个，这两者之间的关心完全取决于编程语言。而Rust选择的是1:1模型，也就是Rust中创建的1个线程，对应的就是实际操作系统的一个线程。

* 线程的创建
    
    * `thread::spawn` 返回一个JoinHandle, 可以等待当前线程结束或do something else
        
    
    ```rust
    use std::thread;
    use std::time::Duration;
    
    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        });
    
        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    
    		// join会等待线程的结束，会成功打印1-9，若没有则会答应出1-4（因为主线程提前退出）
        handle.join().unwrap();
    }
    ```
    
    ```rust
    use std::thread;
    use std::time::Duration;
    
    fn main() {
        let handle = thread::spawn(|| {
            for i in 1..10 {
                println!("hi number {} from the spawned thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        });
    
        // 若放在中间，join会等待新thread完全运行完成再执行主线程代码
        handle.join().unwrap();
    
        for i in 1..5 {
            println!("hi number {} from the main thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    }
    ```
    

![](https://file.notion.so/f/f/11f66c5f-6043-406b-b492-4dc267645668/b51dbec2-edf3-4e11-b885-b3c94e9d2366/Untitled.png?id=39ae95a3-857a-4467-9d88-49943ef5630c&table=block&spaceId=11f66c5f-6043-406b-b492-4dc267645668&expirationTimestamp=1702972800000&signature=rcgqqqSiRhQI-RzpP1YrqJ3JHYA4wDamnpXH9DxjLVg&downloadName=Untitled.png align="left")

上图说明了Concurrent和Parallel的区别

排队的队列对应现成，咖啡机对应CPU核心。

* 并发(Concurrent) 是多个队列使用同一个咖啡机，然后两个队列轮换着使用（未必是 1:1 轮换，也可能是其它轮换规则），最终每个人都能接到咖啡
    
* 并行(Parallel) 是每个队列都拥有一个咖啡机，最终也是每个人都能接到咖啡，但是效率更高，因为同时可以有两个人在接咖啡
    

并发和并行都是对“多任务”处理的描述，其中并发是轮流处理，而并行是同时处理。

## Publish

1. `cargo package` 检查打包后的crate文件
    
2. `cargo publish`
    

### 额外信息

* `cargo.toml`区分release与dev的profile设置
    
    通过`[profile.*]`语法区分环境，例如
    
    ```toml
    [profile.dev]
    opt-level = 0
    
    [profile.release]
    opt-level = 3
    ```
    
* opt-level设置
    
    dev默认0，release默认3，range为0-3，更大的值代表Rustc会做更多的optimizations，所以也相对更慢。
    
* Crate中的文档编写
    
    通过`///`来表示注释为Rust Doc。例如：
    
    ```rust
    //! # My Crate
    //!
    //! `my_crate` is a collection of utilities to make performing certain
    //! calculations more convenient.
    
    /// Adds one to the number given.
    ///
    /// # Examples
    ///
    /// ```
    /// let arg = 5;
    /// let answer = my_crate::add_one(arg);
    ///
    /// assert_eq!(6, answer);
    /// ```
    pub fn add_one(x: i32) -> i32 {
        x + 1
    }
    ```
    
    * 使用`cargo doc`生成doc，使用`cargo doc —open`打开文档预览。
        
    * 文档中的`assert_eq!`会作为一个独立的测试用例在使用`cargo test`时被调用检测
        
    * 使用`//!` 表示当前包的信息，会显示在文档最上方。
        
* Publish相关
    
    `cargo login`: [https://doc.rust-lang.org/book/ch14-02-publishing-to-crates-io.html#setting-up-a-cratesio-account](https://www.flenker.blog/hecto-chapter-1/)
    
    由于使用了字节的Registry源，登录时需要带上—registry crates-io
    
    `cargo login xxxxxxxxxxyourapitokenxxxx --registry crates-io`
    
    使用cargo publish进行crate发布。若没有进行特别配置，每次需要带上`—registry crates-io` 的命令行设置，可以通过建立`.cargo/config.toml` 并声明默认发布registry，例如：
    
    ```toml
    # .cargo/config.toml
    [registry]
    default = "crates-io"
    ```
    

## Suggestions

* 跟着这个学习非常有效 [https://www.flenker.blog/hecto-chapter-1/](https://www.flenker.blog/hecto-chapter-1/)
    
* rust-clippy是rust的lint工具
    
    * `cargo clippy -- -W clippy::pedantic`针对所有lint rules的一次校验
        
    * 由于cargo每次只compile changed files, 所以在执行上面命令时，可使用`cargo clean`清理build文件，让lint作用于所有文件
        

## 一些常用方法记录

* 类三元
    
    Rust中不存在三元，一些可用的替代方案如下
    
    1. 使用if else来组成block expression，将返回值返回给results
        
    
    ```rust
    let results = if config.ignore_case {
        search_case_insensitive(&config.query, &contents)
    } else {
        search(&config.query, &contents)
    };
    ```
    
    1. 使用pattern matching
        
    
    ```rust
    let results = match config.ignore_case {
        true => search_case_insensitive(&config.query, &contents),
        false => search(&config.query, &contents),
    };
    ```
    
    1. 使用if let的pattern matching
        
    
    ```rust
    println!(
        "The spawned thread is {}",
        if let true = handler.is_finished() { "finished" } else { "not finished" }
    );
    ```
    
* 防治数字overflow的add方法
    
    `saturating_add`
    
* Slice String
    
    ```rust
    let width = 5;
    let sliced = &String::from("xxxxxxxx")[..width]
    ```
    
* 获取当前crate的元数据
    
    ```rust
    macro_rules! print_env {
        ($name: expr) => {
            println!("{}={}", $name, env!($name))
        };
    }
    
    print_env!("CARGO_PKG_VERSION_MAJOR");
    print_env!("CARGO_MANIFEST_DIR");
    print_env!("CARGO_PKG_AUTHORS");
    print_env!("CARGO_PKG_DESCRIPTION");
    print_env!("CARGO_PKG_HOMEPAGE");
    print_env!("CARGO_PKG_NAME");
    print_env!("CARGO_PKG_REPOSITORY");
    print_env!("CARGO_PKG_VERSION");
    print_env!("CARGO_PKG_VERSION_MAJOR");
    print_env!("CARGO_PKG_VERSION_MINOR");
    print_env!("CARGO_PKG_VERSION_PATCH");
    print_env!("CARGO_PKG_VERSION_PRE");
    ```
    
* 新建、执行cargo等相关命令
    
    * `cargo new`创建新的cargo，`cargo run`运行cargo
        
* 查看crate相关文档
    
    * crate内执行`crago doc —-open`会打开当前crate内相关引用crate的文档
        
* 更换国内源
    
    * 参考：[http://rsproxy.cn/](https://www.flenker.blog/hecto-chapter-1/)
        
* 工程中（workspace)中有多个cargo projects，相关配置
    
    * 工程根目录新建`Cargo.toml`并声明workspaces-members 参考：[https://github.com/citrus327/give-rust-one-more-chance/blob/main/Cargo.toml](https://www.flenker.blog/hecto-chapter-1/)
        
    * 若想运行特定cargo project，可以进入cargo文件夹后执行`cargo run`，或者在project root使用`cargo run --bin [cargo-name]`来执行
        
* `turbofish`语法 （涡轮鱼）