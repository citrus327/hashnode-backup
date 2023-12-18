---
title: "一些手写代码的代码集合"
datePublished: Mon Aug 20 2018 16:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clqajgnaj000a07la26czamk5
slug: 5lia5lqb5oml5yaz5luj56cb55qe5luj56cb6zug5zci
tags: javascript

---

## Array.prototype.filter

```jsx
function filter(source, method) {
  let result = [];

  source.forEach((curr, index, src) => {
    if (method(curr, index, src)) {
      result.push(curr);
    }
  });

  return result;
}

let arr = [{ name: "hao" }, { name: "peng" }, { name: "derek" }];

let result = filter(arr, (item) => item.name === "derek");

console.log(result);
```

## Array.prototype.reduce

```jsx
function reduce(source, method, acc) {
  if (acc === undefined) {
    throw new Error("accumlator is undefined");
  }

  let copy = acc;

  source.forEach((curr, index, src) => {
    copy = method(copy, curr, index, src);
  });

  return copy;
}

let arr = [1, 2, 3];

let result = reduce(
  arr,
  (acc, curr, index, src) => {
    console.log(acc, curr, index, src);
    return (acc += curr);
  },
  0
);

console.log(result);
```

## Function.prototype.bind

```jsx
function bind(method, ctx) {
  let parameters = [...arguments];
  parameters.shift();
  parameters.shift();
  return method.apply(ctx, parameters);
}

function test() {
  var module = {
    x: "old",
    getX: function (text1, text2) {
      console.log(text1, text2, this.x);
      return this.x;
    },
  };

  var newOne = {
    x: "new",
  };

  console.log(module.getX("1", "2"));
  console.log(module.getX.bind(newOne, "3", "4")());
  console.log("---------------------------");
  console.log(bind(module.getX, newOne, "3", "4"));
}

test();
```

## debounce

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

const target = function () {
  console.log(5);
};
const result = debounce(target, 3000);

result();
result();
result();
```

## throttle

```jsx
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

const target = function () {
  console.log(5);
};
const result = throttle(target, 3000);

result();
result();
result();
```

## inheritance

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
```

## Promise

```jsx
const STATUS = {
  PENDING: "PENDING",
  RESOLVED: "RESOLVED",
  REJECTED: "REJECTED",
};

function isCallable(value) {
  return value && typeof value === "function";
}

function MyPromise(executor) {
  this.status = STATUS.PENDING;
  this.value = undefined;
  this.reason = undefined;

  this.onResolvedTaskQueue = [];
  this.onRejectedTaskQueue = [];

  const resolver = (value) => {
    if (this.status === STATUS.PENDING) {
      this.onResolvedTaskQueue.forEach((task) => {
        if (isCallable(task)) {
          task(value);
        }
      });
      this.status = STATUS.RESOLVED;
      this.value = value;
      this.reason = undefined;
    }
  };

  const rejector = (reason) => {
    if (this.status === STATUS.PENDING) {
      this.onRejectedTaskQueue.forEach((task) => {
        if (isCallable(task)) {
          task(reason);
        }
      });
      this.status = STATUS.REJECTED;
      this.value = undefined;
      this.reason = reason;
    }
  };

  try {
    executor(resolver, rejector);
  } catch (err) {
    // this.status === STATUS.REJECTED
    // this.reason = err
  }
}

MyPromise.prototype.then = function (method) {
  if (this.status === STATUS.PENDING) {
    this.onResolvedTaskQueue.push(method);
  } else {
    if (this.status === STATUS.RESOLVED && isCallable(method)) {
      method(this.value);
    }
    // return MyPromise.resolve(returnValue)
  }
};

MyPromise.prototype.catch = function (method) {
  if (this.status === STATUS.PENDING) {
    this.onRejectedTaskQueue.push(method);
  } else {
    if (this.status === STATUS.REJECTED && isCallable(method)) {
      method(this.reason);
    }
  }
};

MyPromise.resolve = function (value) {
  return new MyPromise((resolve, reject) => {
    resolve(value);
  });
};

MyPromise.reject = function (reason) {
  return new MyPromise((resolve, reject) => {
    reject(reason);
  });
};

function case1() {
  new MyPromise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
      resolve(2);
    }, 1000);
  }).then((res) => {
    console.log(res);
    return 3;
  });
}
case1();
```

## Event Emitter

```jsx
function EventEmitter() {
  this._events = {};
}

EventEmitter.prototype.$on = function (eventName, handler) {
  this._events[eventName] = handler;
};

EventEmitter.prototype.$emit = function (eventName, ...args) {
  if (eventName in this._events) {
    const handler = this._events[eventName];
    if (handler && typeof handler === "function") {
      handler.apply(this, args);
    }
  }
};

const instance = new EventEmitter();

function test() {
  this.name = "abc";

  instance.$on("test", () => {
    console.log(this.name);
  });

  instance.$emit("test");
}

person();
```

## curry 化的 plus 函数

```jsx
function plus(num) {
  let base = num || 0;
  return function (num2) {
    let temp = num2 || 0;
    base += temp;
    if (num2 === undefined) {
      return base;
    }
    return plus(base);
  };
}

function test() {
  const result = plus(1)(2)(3)();

  console.log(result);
}

test();
```

## fibonacci