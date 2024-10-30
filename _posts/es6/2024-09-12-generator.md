---
layout: post
title: ES6 - Generator 函数
categories: blog
tags:
  - ES6
  - Generator
---

> 本文是对[《ECMAScript 6 入门 - Generator 函数的语法》](https://es6.ruanyifeng.com/#docs/generator) 和[《ECMAScript 6 入门 - Generator 函数的异步应用》](https://es6.ruanyifeng.com/#docs/generator-async) 两章中知识点的摘录，请访问原文，获取更多详细信息。

## 一、简介

### 1. 基本概念

Generator 函数是 ES6 提供的一种异步编程解决方案。

- **状态机**：语法上，可以认为 Generator 函数是一个状态机，封装了多个内部状态。
- **遍历器对象生成函数**：Generator 函数还是一个遍历器对象生成函数。它的返回值是一个遍历器对象，可以依次遍历 Generator 函数内部的每个状态。

相比于普通函数，Generator 函数有两个特征。

- function 关键字后有个星号（`*`）。
- 函数体内部使用 `yield` 表达式，产生不同的内部状态。

Generator 函数的调用方法与普通函数一样，调用 Generator 函数后，该函数并不执行，而是返回一个指向内部状态的遍历器对象（Iterator Object）。调用遍历器对象的 `next` 方法，指针移向下一个状态，直到遇到下一个 `yield` 表达式（或 `return` 语句）为止。换言之，Generator 函数是分段执行的，**`yield` 表达式是暂停执行的标记，而 `next` 方法是恢复执行的标记**。

每次调用 `next` 方法都会返回一个对象，对象的 `value` 属性指向当前 `yield` 表达式后面的值，`done` 属性是一个布尔值，表示是否遍历完成。

### 2. yield 表达式

遍历器对象的 `next` 方法的运行逻辑如下。

1. 遇到 `yield` 表达式，就暂停执行后面的操作，并将紧跟在 `yield` 后面的那个表达式的值，作为返回的对象的 `value` 属性值。
2. 下一次调用 `next` 方法时，再继续往下执行，直到遇到下一个 `yield` 表达式。
3. 如果没有再遇到新的 `yield` 表达式，就一直运行到函数结束，直到 `return` 语句为止，并将 `return` 语句后面的表达式的值，作为返回的对象的 `value` 属性值。
4. 如果该函数没有 `return` 语句，则返回的对象的 `value` 属性值为 `undefined`。

注意，`yield` 表达式只能用在 Generator 函数里面，用在其他地方会报错。

### 3. 与 Iterator 接口的关系

对象的 `Symbol.iterator` 方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。由于 Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的 `Symbol.iterator` 属性，从而使得该对象具有 Iterator 接口。

Generator 函数执行后返回的遍历器对象，也具有 `Symbol.iterator` 属性，执行后返回自身。

```javascript
function* gen(){
  // some code
}

var g = gen();
g[Symbol.iterator]() === g // true
```

## 二、next 方法的参数

**`next` 方法可以带一个参数，该参数会被当作上一个 `yield` 表达式的返回值**。Generator 函数从暂停状态到恢复运行，它的上下文状态（context）是不变的。通过 `next` 方法的参数，就可以在 Generator 函数开始运行之后，继续向函数体内部注入值，从而调整函数行为。

```javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
}

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}
```

上面代码中，第二次运行 `next` 方法的时候不带参数，导致 `y` 的值等于 `2 * undefined`（即 `NaN`），除以 `3` 以后还是 `NaN`，因此返回对象的 `value` 属性也等于 `NaN`。第三次运行 `next` 方法的时候不带参数，所以 `z` 等于 `undefined`，返回对象的 `value` 属性等于 `5 + NaN + undefined`，即 `NaN`。

```javascript
var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }
b.next(13) // { value:42, done:true }
```

- 第一次调用 `b` 的 `next` 方法时，返回 `x+1` 的值 `6`。
- 第二次调用 `next` 方法，将上一次 `yield` 表达式的值设为 `12`，因此 `y` 等于 `24`，返回 `y / 3` 的值 `8`。
- 第三次调用 `next` 方法，将上一次 `yield` 表达式的值设为 `13`，因此 `z` 等于 `13`，这时 `x` 等于 `5`，`y` 等于 `24`，所以 `return` 语句的值等于 `42`。

注意，由于 `next` 方法的参数表示上一个 `yield` 表达式的返回值，所以在第一次使用 `next` 方法时，传递参数是无效的。

## 三、for...of 循环

**`for...of` 循环、扩展运算符（`...`）、解构赋值和 `Array.from` 方法内部调用的，都是遍历器接口**。这意味着，它们都可以将 Generator 函数返回的 Iterator 对象，作为参数。

```javascript
function* numbers () {
  yield 1
  yield 2
  return 3
  yield 4
}

// 扩展运算符
[...numbers()] // [1, 2]

// 解构赋值
let [x, y] = numbers();
x // 1
y // 2

// Array.from 方法
Array.from(numbers()) // [1, 2]

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```

一旦 `next` 方法的返回对象的 `done` 属性为 `true`，`for...of` 循环就会中止，**且不包含该返回对象**，所以上面代码的 `return` 语句返回的 `3`，不包括在 `for...of` 循环之中（也包括其他三种操作）。

原生的 JavaScript 对象没有遍历接口，无法使用 `for...of` 循环，可以通过 Generator 函数为它加上这个接口。

```javascript
function* objectEntries(obj) {
  let keys = Reflect.ownKeys(obj);
  for (let key of keys) {
    yield [key, obj[key]];
  }
}

let obj = { name: 'Jane' };
for (let [key, value] of objectEntries(obj)) {
  console.log(`${key}: ${value}`);
}
// name: Jane
```

加上遍历器接口的另一种写法是，将 Generator 函数加到对象的 `Symbol.iterator` 属性上面。

```javascript
function* objectEntries() {
  let keys = Object.keys(this);
  for (let key of keys) {
    yield [key, this[key]];
  }
}

let obj = { name: 'Jane' };
obj[Symbol.iterator] = objectEntries;
for (let [key, value] of obj) {
  console.log(`${key}: ${value}`);
}
// name: Jane
```

## 四、Generator.prototype.throw()

- Generator 函数返回的遍历器对象有个 `throw` 方法，可以在函数体外抛出错误，然后在 Generator 函数体内捕获。`throw` 方法可以接受一个参数，该参数会被 `catch` 语句接收。（注意，遍历器对象的 `throw` 方法和全局的 `throw` 命令是两个不同的操作，两者并无关系。）

  ```javascript
  var g = function* () {
    try {
      yield;
    } catch (e) {
      console.log(e);
    }
  };

  var i = g();
  i.next();
  i.throw(new Error('出错了！'));
  // Error: 出错了！(…)
  ```

- 如果 Generator 函数内部没有部署 `try...catch` 代码块，那么 `throw` 方法抛出的错误，将被外部 `try...catch` 代码块捕获。如果 Generator 函数内部和外部，都没有部署 `try...catch` 代码块，那么程序将报错并中断执行。

  ```javascript
  var g = function* () {
    while (true) {
      yield;
      console.log('内部捕获', e);
    }
  };

  var i = g();
  i.next();

  try {
    i.throw('a');
    i.throw('b');
  } catch (e) {
    console.log('外部捕获', e);
  }
  // 外部捕获 a
  ```

- `throw` 方法抛出的错误要被内部捕获，前提是必须至少执行过一次 `next` 方法。这种行为其实很好理解，因为第一次执行 `next` 方法，等同于启动执行 Generator 函数的内部代码，否则 Generator 函数还没有开始执行，这时 `throw` 方法抛错只可能抛出在函数外部。
- `throw` 方法被内部捕获以后，会附带执行到下一条 `yield` 表达式，这种情况下等同于执行一次 `next` 方法。

  ```javascript
  var gen = function* gen(){
    try {
      yield 1;
    } catch (e) {
      yield 2;
    }
    yield 3;
  }

  var g = gen();
  g.next() // { value:1, done:false }
  g.throw() // { value:2, done:false }
  g.next() // { value:3, done:false }
  g.next() // { value:undefined, done:true }
  ```

  上面代码中，`g.throw` 方法被内部捕获以后，等同于执行了一次 `next` 方法，所以返回 `{ value:2, done:false }`。另外，也可以看到，只要 Generator 函数内部部署了 `try...catch` 代码块，那么遍历器的 `throw` 方法抛出的错误，不影响下一次遍历。

- **Generator 函数体外抛出的错误，可以在函数体内捕获；Generator 函数体内抛出的错误，也可以被函数体外的 `catch` 捕获**。一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了。如果此后还调用 `next` 方法，将返回 `{ value: undefined, done: true }` 对象，即 JavaScript 引擎认为这个 Generator 已经运行结束了。

## 五、Generator.prototype.return()

- Generator 函数返回的遍历器对象有个 `return()` 方法，可以返回给定的值，并且终结遍历 Generator 函数。如果 `return()` 方法调用时，不提供参数，则返回值的 `value` 属性为 `undefined`。

  ```javascript
  function* gen() {
    yield 1;
    yield 2;
    yield 3;
  }

  var g = gen();
  g.next()        // { value: 1, done: false }
  g.return('foo') // { value: "foo", done: true }
  g.next()        // { value: undefined, done: true }
  ```

- 如果 Generator 函数内部有 `try...finally` 代码块，且正在执行 `try` 代码块，那么 `return()` 方法会导致立刻进入 `finally` 代码块，执行完以后，整个函数才会结束。

  ```javascript
  function* numbers () {
    yield 1;
    try {
      yield 2;
      yield 3;
    } finally {
      yield 4;
      yield 5;
    }
    yield 6;
  }

  var g = numbers();
  g.next() // { value: 1, done: false }
  g.next() // { value: 2, done: false }
  g.return(7) // { value: 4, done: false }
  g.next() // { value: 5, done: false }
  g.next() // { value: 7, done: true }
  ```

  上面代码中，调用 `return()` 方法后，就开始执行 `finally` 代码块，不执行 `try` 里面剩下的代码了，然后等到 `finally` 代码块执行完，再返回 `return()` 方法指定的返回值。

## 六、next()、throw()、return() 的共同点

`next()`、`throw()`、`return()` 这三个方法本质上是同一件事，它们的作用都是让 Generator 函数恢复执行，并且使用不同的语句替换 `yield` 表达式。

- `next()` 是将 `yield` 表达式替换成一个值。
- `throw()` 是将 `yield` 表达式替换成一个 `throw` 语句。
- `return()` 是将 `yield` 表达式替换成一个 `return` 语句。

## 七、yield* 表达式

ES6 提供了 `yield*` 表达式，用来在一个 Generator 函数里面执行另一个 Generator 函数。

```javascript
function* foo() {
  yield 'a';
  yield 'b';
}

function* bar() {
  yield 'x';
  yield* foo();
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}

// 等同于
function* bar() {
  yield 'x';
  for (let v of foo()) {
    yield v;
  }
  yield 'y';
}

for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
```

- `yield*` 后面的 Generator 函数，如果没有 `return` 语句，等同于在 Generator 函数内部部署一个 `for...of` 循环。反之，如果被代理的 Generator 函数有 `return` 语句，那么就可以向代理它的 Generator 函数返回数据。

  ```javascript
  function* foo() {
    yield 2;
    yield 3;
    return "foo";
  }

  function* bar() {
    yield 1;
    var v = yield* foo();
    console.log("v: " + v);
    yield 4;
  }

  var it = bar();
  it.next()
  // {value: 1, done: false}
  it.next()
  // {value: 2, done: false}
  it.next()
  // {value: 3, done: false}
  it.next();
  // "v: foo"
  // {value: 4, done: false}
  it.next()
  // {value: undefined, done: true}
  ```

- 如果 `yield*` 后面跟着一个数组，由于数组原生支持遍历器，因此就会遍历数组成员。实际上，任何数据结构只要有 Iterator 接口，就可以被 `yield*` 遍历。

  ```javascript
  function* gen() {
    yield* ["a", "b", "c"];
  }
  gen().next() // { value:"a", done:false }

  let read = (function* () {
    yield 'hello';
    yield* 'hello';
  })();

  read.next().value // "hello"
  read.next().value // "h"
  ```

  上面代码中，`yield` 表达式返回整个字符串，`yield*` 语句返回单个字符。因为字符串具有 Iterator 接口，所以被 `yield*` 遍历。

- `yield*` 命令可以很方便地取出嵌套数组的所有成员。由于扩展运算符 `...` 默认调用 Iterator 接口，所以上面这个函数也可以用于嵌套数组的平铺。

  ```javascript
  function* iterTree(tree) {
    if (Array.isArray(tree)) {
      for(let i=0; i < tree.length; i++) {
        yield* iterTree(tree[i]);
      }
    } else {
      yield tree;
    }
  }

  const tree = [ 'a', ['b', 'c'], ['d', 'e'] ];
  for(let x of iterTree(tree)) {
    console.log(x);
  }
  // a
  // b
  // c
  // d
  // e

  [...iterTree(tree)] // ["a", "b", "c", "d", "e"]
  ```

## 八、作为对象属性的 Generator 函数

如果一个对象的属性是 Generator 函数，可以简写成下面的形式。

```javascript
let obj = {
  * generator() {
    ···
  }
};

// 等同于
let obj = {
  generator: function* () {
    // ···
  }
};
```

## 九、Generator 函数的 this

Generator 函数不能用作构造函数，也不能跟 new 命令一起用。

```javascript
function* g() {
  this.a = 11;
}

let obj = g();
obj.next();
obj.a // undefined
```

## 十、含义

### 10.1 Generator 与状态机

Generator 是实现状态机的最佳结构。

```javascript
// ES5 实现
var ticking = true;
var clock = function() {
  if (ticking)
    console.log('Tick!');
  else
    console.log('Tock!');
  ticking = !ticking;
}

// Generator 实现
var clock = function* () {
  while (true) {
    console.log('Tick!');
    yield;
    console.log('Tock!');
    yield;
  }
};
```

上面的 Generator 实现与 ES5 实现对比，可以看到少了用来保存状态的外部变量 ticking，这样就更简洁，更安全（状态不会被非法篡改）、更符合函数式编程的思想，在写法上也更优雅。Generator 之所以可以不用外部变量保存状态，是因为它本身就包含了一个状态信息，即目前是否处于暂停态。

### 10.2 Generator 与协程

#### （1）协程与子例程的差异

传统的“子例程”（subroutine）采用堆栈式“后进先出”的执行方式，只有当调用的子函数完全执行完毕，才会结束执行父函数。协程与其不同，多个线程（单线程情况下，即多个函数）可以并行执行，但是只有一个线程（或函数）处于正在运行的状态，其他线程（或函数）都处于暂停态（suspended），线程（或函数）之间可以交换执行权。也就是说，一个线程（或函数）执行到一半，可以暂停执行，将执行权交给另一个线程（或函数），等到稍后收回执行权的时候，再恢复执行。这种可以并行执行、交换执行权的线程（或函数），就称为协程。

从实现上看，在内存中，子例程只使用一个栈（stack），而协程是同时存在多个栈，但只有一个栈是在运行状态，也就是说，协程是以多占用内存为代价，实现多任务的并行。

#### （2）协程与普通线程的差异

不难看出，协程适合用于多任务运行的环境。在这个意义上，它与普通的线程很相似，都有自己的执行上下文、可以分享全局变量。它们的不同之处在于，同一时间可以有多个线程处于运行状态，但是运行的协程只能有一个，其他协程都处于暂停状态。此外，普通的线程是抢先式的，到底哪个线程优先得到资源，必须由运行环境决定，但是协程是合作式的，执行权由协程自己分配。

由于 JavaScript 是单线程语言，只能保持一个调用栈。引入协程以后，每个任务可以保持自己的调用栈。这样做的最大好处，就是抛出错误的时候，可以找到原始的调用栈。不至于像异步操作的回调函数那样，一旦出错，原始的调用栈早就结束。

Generator 函数是 ES6 对协程的实现，但属于不完全实现。Generator 函数被称为“半协程”（semi-coroutine），意思是只有 Generator 函数的调用者，才能将程序的执行权还给 Generator 函数。如果是完全执行的协程，任何函数都可以让暂停的协程继续执行。

如果将 Generator 函数当作协程，完全可以将多个需要互相协作的任务写成 Generator 函数，它们之间使用yield表达式交换控制权。

### 10.3 Generator 与上下文

JavaScript 代码运行时，会产生一个全局的上下文环境（context，又称运行环境），包含了当前所有的变量和对象。然后，执行函数（或块级代码）的时候，又会在当前上下文环境的上层，产生一个函数运行的上下文，变成当前（active）的上下文，由此形成一个上下文环境的堆栈（context stack）。

这个堆栈是“后进先出”的数据结构，最后产生的上下文环境首先执行完成，退出堆栈，然后再执行完成它下层的上下文，直至所有代码执行完成，堆栈清空。

Generator 函数不是这样，它执行产生的上下文环境，一旦遇到yield命令，就会暂时退出堆栈，但是并不消失，里面的所有变量和对象会冻结在当前状态。等到对它执行next命令时，这个上下文环境又会重新加入调用栈，冻结的变量和对象恢复执行。

```javascript
function* gen() {
  yield 1;
  return 2;
}

let g = gen();

console.log(
  g.next().value,
  g.next().value,
);
```

上面代码中，第一次执行 `g.next()` 时，Generator 函数 `gen` 的上下文会加入堆栈，即开始运行 `gen` 内部的代码。等遇到 `yield 1` 时，`gen` 上下文退出堆栈，内部状态冻结。第二次执行 `g.next()` 时，`gen` 上下文重新加入堆栈，变成当前的上下文，重新恢复执行。

## 十一、应用

Generator 可以暂停函数执行，返回任意表达式的值。这种特点使得 Generator 有多种应用场景。

### 1. 异步操作的同步化表达

### 2. 控制流管理

### 3. 部署 Iterator 接口

利用 Generator 函数，可以在任意对象上部署 Iterator 接口。

```javascript
function* iterEntries(obj) {
  let keys = Object.keys(obj);
  for (let i=0; i < keys.length; i++) {
    let key = keys[i];
    yield [key, obj[key]];
  }
}

let myObj = { foo: 3, bar: 7 };
for (let [key, value] of iterEntries(myObj)) {
  console.log(key, value);
}
// foo 3
// bar 7
```

### 4. 作为数据结构

**Generator 使得数据或者操作，具备了类似数组的接口**。Generator 可以看作是数据结构，更确切地说，可以看作是一个数组结构，因为 Generator 函数可以返回一系列的值，这意味着它可以对任意表达式，提供类似数组的接口。

```javascript
function* doStuff() {
  yield fs.readFile.bind(null, 'hello.txt');
  yield fs.readFile.bind(null, 'world.txt');
  yield fs.readFile.bind(null, 'and-such.txt');
}

for (task of doStuff()) {
  // task 是一个函数，可以像回调函数那样使用它
}
```

上面代码就是依次返回三个函数，但是由于使用了 Generator 函数，导致可以像处理数组那样，处理这三个返回的函数。然后，就可以使用 `for...of` 循环进行处理。

## 十二、Generator 函数的异步应用

### 1. 传统方法

ES6 诞生以前，异步编程的方法，大概有下面四种。

- 回调函数
- 事件监听
- 发布/订阅
- Promise 对象

Generator 函数将 JavaScript 异步编程带入了一个全新的阶段。

### 2. 基本概念

JavaScript 语言对异步编程的实现，是使用回调函数。回调函数的英语名字 `callback`，直译过来就是"重新调用"。回调函数的问题是，容易出现多个回调函数嵌套。即所谓的"回调函数地狱"（callback hell）。

Promise 对象就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套，改成链式调用。Promise 的最大问题是代码冗余，原来的任务被 Promise 包装了一下，不管什么操作，一眼看去都是一堆 `then`，原来的语义变得很不清楚。

### 3. Generator 函数

#### 3.1 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做**协程**（coroutine），意思是多个线程互相协作，完成异步任务。协程有点像函数，又有点像线程。它的运行流程大致如下。

1. 第一步，协程 `A` 开始执行。
2. 第二步，协程 `A` 执行到一半，进入暂停，执行权转移到协程 `B`。
3. 第三步，（一段时间后）协程 `B` 交还执行权。
4. 第四步，协程 `A` 恢复执行。

上面流程的协程 `A`，就是异步任务，因为它分成两段（或多段）执行。

```javascript
function* asyncJob() {
  // ...其他代码
  var f = yield readFile(fileA);
  // ...其他代码
}
```

上面代码的函数 `asyncJob` 是一个协程，它的奥妙就在其中的 `yield` 命令。它表示执行到此处，执行权将交给其他协程。也就是说，`yield` 命令是异步两个阶段的分界线。

协程遇到 `yield` 命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作。

#### 3.2 协程的 Generator 函数实现

**Generator 函数是协程在 ES6 的实现，最大特点是可以交出函数的执行权（即暂停执行）**。整个 Generator 函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 `yield` 语句注明。

Generator 函数的 `next` 方法的作用是分阶段执行 Generator 函数。每次调用 `next` 方法，会返回一个对象，表示当前阶段的信息（`value` 属性和 `done` 属性）。`value` 属性是 `yield` 语句后面表达式的值，表示当前阶段的值；`done` 属性是一个布尔值，表示 Generator 函数是否执行完毕，即是否还有下一个阶段。

#### 3.3 Generator 函数的数据交换和错误处理

Generator 函数可以暂停执行和恢复执行，这是它能封装异步任务的根本原因。除此之外，使它可以作为异步编程的完整解决方案：函数体内外的数据交换和错误处理机制。

- 对于函数体内外的数据交换，`next` 返回值的 `value` 属性，是 Generator 函数向外输出数据；`next` 方法还可以接受参数，向 Generator 函数体内输入数据。
- 对于错误处理机制，可以通过 Generator 函数返回的遍历器对象的 [throw](#四generatorprototypethrow) 方法。

#### 3.4 异步任务的封装

```javascript
var fetch = require('node-fetch');

function* gen(){
  var url = 'https://api.github.com/users/github';
  var result = yield fetch(url);
  console.log(result.bio);
}
```

上面代码中，Generator 函数封装了一个异步操作。就像前面说过的，这段代码非常像同步操作，除了加上了 `yield` 命令。执行这段代码的方法如下。

```javascript
var g = gen();
var result = g.next();

result.value.then(function(data){
  return data.json();
}).then(function(data){
  g.next(data);
});
```

可以看到，虽然 Generator 函数将异步操作表示得很简洁，但是流程管理却不方便（即何时执行第一阶段、何时执行第二阶段）。

### 4. Thunk 函数

Thunk 函数是**自动执行 Generator 函数**的一种方法。

#### 4.1 参数的求值策略

在编程语言的早期，一个争论的焦点是**求值策略**，即函数的参数到底应该何时求值。

```javascript
var x = 1;
function f(m) {
  return m * 2;
}
f(x + 5)
```

- 一种意见是**传值调用**（call by value），即在进入函数体之前，就计算 `x + 5` 的值（等于 `6`），再将这个值传入函数 `f`。C 语言就采用这种策略。

  ```javascript
  f(x + 5)
  // 传值调用时，等同于
  f(6)
  ```

- 另一种意见是**传名调用**（call by name），即直接将表达式 `x + 5` 传入函数体，只在用到它的时候求值。Haskell 语言采用这种策略。

  ```javascript
  f(x + 5)
  // 传名调用时，等同于
  (x + 5) * 2
  ```

两种方式各有利弊。传值调用比较简单，但是对参数求值的时候，实际上还没用到这个参数，有可能造成性能损失。

#### 4.2 Thunk 函数的含义

编译器的**传名调用**实现，往往是将参数放到一个临时函数之中，再将这个临时函数传入函数体。这个临时函数就叫做 **Thunk 函数**。Thunk 函数是**传名调用**的一种实现策略，用来替换某个表达式。

```javascript
function f(m) {
  return m * 2;
}
f(x + 5);

// 等同于
var thunk = function () {
  return x + 5;
};
function f(thunk) {
  return thunk() * 2;
}
```

#### 4.3 JavaScript 语言的 Thunk 函数

JavaScript 语言是**传值调用**，它的 Thunk 函数含义有所不同。在 JavaScript 语言中，Thunk 函数替换的不是表达式，而是**多参数函数**，将其替换成一个**只接受回调函数作为参数的单参数函数**。

```javascript
// 正常版本的 readFile（多参数版本）
fs.readFile(fileName, callback);

// Thunk 版本的 readFile（单参数版本）
var Thunk = function (fileName) {
  return function (callback) {
    return fs.readFile(fileName, callback);
  };
};

var readFileThunk = Thunk(fileName);
readFileThunk(callback);
```

上面代码中，`fs` 模块的 `readFile` 方法是一个多参数函数，两个参数分别为文件名和回调函数。经过转换器处理，它变成了一个单参数函数，只接受回调函数作为参数。这个单参数版本，就叫做 Thunk 函数。

任何函数，只要参数有回调函数，就能写成 Thunk 函数的形式。下面是一个简单的 Thunk 函数转换器。

```javascript
// ES5 版本
var Thunk = function(fn) {
  return function () {
    var args = Array.prototype.slice.call(arguments);
    return function (callback){
      args.push(callback);
      return fn.apply(this, args);
    }
  };
};

// ES6 版本
const Thunk = function(fn) {
  return function (...args) {
    return function (callback) {
      return fn.call(this, ...args, callback);
    }
  };
};

// 使用上面的转换器，生成 fs.readFile 的 Thunk 函数。
var readFileThunk = Thunk(fs.readFile);
readFileThunk(fileA)(callback);
```

#### 4.4 Thunkify 模块

生产环境的转换器，建议使用 [Thunkify](https://github.com/tj/node-thunkify) 模块。下面是 Thunkify 的源码。

```javascript
function thunkify(fn){
  assert('function' == typeof fn, 'function required');

  return function() {
    var args = new Array(arguments.length);
    var ctx = this;

    for(var i = 0; i < args.length; ++i) {
      args[i] = arguments[i];
    }

    return function(done) {
      var called;

      args.push(function() {
        if (called) return;
        called = true;
        done.apply(null, arguments);
      });

      try {
        fn.apply(ctx, args);
      } catch (err) {
        done(err);
      }
    }
  }
};
```

Thunkify 的源码与上一节那个简单的转换器非常像。它的源码主要多了一个检查机制，变量 `called` 确保回调函数只运行一次。这样的设计与下文的 Generator 函数相关。请看下面的例子。

```javascript
function f(a, b, callback){
  var sum = a + b;
  callback(sum);
  callback(sum);
}

var ft = thunkify(f);
var print = console.log.bind(console);
ft(1, 2)(print);
// 3
```

上面代码中，由于 `thunkify` 只允许回调函数执行一次，所以只输出一行结果。

#### 4.5 Generator 函数的流程管理

Thunk 函数可以用于 Generator 函数的自动流程管理（也就是说，Generator 函数现在可以自动执行了）。以读取文件为例。下面的 Generator 函数封装了两个异步操作。

```javascript
var fs = require('fs');
var thunkify = require('thunkify');
var readFileThunk = thunkify(fs.readFile);

var gen = function* (){
  var r1 = yield readFileThunk('/etc/fstab');
  console.log(r1.toString());
  var r2 = yield readFileThunk('/etc/shells');
  console.log(r2.toString());
};
```

上面代码中，`yield` 命令用于将程序的执行权移出 Generator 函数，那么就需要一种方法，将执行权再交还给 Generator 函数。这种方法就是 Thunk 函数，因为它可以在回调函数里，将执行权交还给 Generator 函数。为了便于理解，我们先看如何手动执行上面这个 Generator 函数。

```javascript
var g = gen();

var r1 = g.next();
r1.value(function (err, data) {
  if (err) throw err;
  var r2 = g.next(data);
  r2.value(function (err, data) {
    if (err) throw err;
    g.next(data);
  });
});
```

仔细查看上面的代码，可以发现 Generator 函数的执行过程，其实是将同一个回调函数，反复传入 `next` 方法的 `value` 属性。这使得我们可以用递归来自动完成这个过程。

#### 4.6 Thunk 函数的自动流程管理

**Thunk 函数可以自动执行 Generator 函数**。下面就是一个基于 Thunk 函数的 Generator 执行器。

```javascript
function run(fn) {
  var gen = fn();
  function next(err, data) {
    var result = gen.next(data);
    if (result.done) return;
    result.value(next);
  }
  next();
}

function* g() {
  // ...
}
run(g);
```

上面代码的 `run` 函数，就是一个 Generator 函数的自动执行器。内部的 `next` 函数就是 Thunk 的回调函数。`next` 函数先将指针移到 Generator 函数的下一步（`gen.next` 方法），然后判断 Generator 函数是否结束（`result.done` 属性），如果没结束，就将 `next` 函数再传入 Thunk 函数（`result.value` 属性），否则就直接退出。

有了这个执行器，执行 Generator 函数方便多了。不管内部有多少个异步操作，直接把 Generator 函数传入 `run` 函数即可。当然，前提是每个异步操作，都要是 Thunk 函数，也就是说，跟在 `yield` 命令后面的必须是 Thunk 函数。

Thunk 函数并不是 Generator 函数自动执行的唯一方案。因为**自动执行的关键是，必须有一种机制，自动控制 Generator 函数的流程，接收和交还程序的执行权**。回调函数可以做到这一点，Promise 对象也可以做到这一点。

### 5. co 模块

#### 5.1 基本用法

[co 模块](https://github.com/tj/co)用于 Generator 函数的自动执行。

```javascript
var gen = function* () {
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

co 模块可以让你不用编写 Generator 函数的执行器。

```javascript
var co = require('co');
co(gen).then(function () {
  console.log('Generator 函数执行完成');
});
```

上面代码中，Generator 函数只要传入 `co` 函数，就会自动执行。`co` 函数返回一个 Promise 对象，因此可以用 `then` 方法添加回调函数。

#### 5.2 co 模块的原理

Generator 就是一个异步操作的容器。它的自动执行需要一种机制，当异步操作有了结果，能够自动交回执行权。两种方法可以做到这一点。

1. **回调函数**：将异步操作包装成 Thunk 函数，在回调函数里面交回执行权。
2. **Promise 对象**：将异步操作包装成 Promise 对象，用 `then` 方法交回执行权。

co 模块其实就是将两种自动执行器（Thunk 函数和 Promise 对象），包装成一个模块。使用 co 的前提条件是，Generator 函数的 `yield` 命令后面，只能是 Thunk 函数或 Promise 对象。如果数组或对象的成员，全部都是 Promise 对象，也可以使用 co，详见后文的例子。

#### 5.3 基于 Promise 对象的自动执行

首先，把 `fs` 模块的 `readFile` 方法包装成一个 Promise 对象。

```javascript
var fs = require('fs');

var readFile = function (fileName){
  return new Promise(function (resolve, reject){
    fs.readFile(fileName, function(error, data){
      if (error) return reject(error);
      resolve(data);
    });
  });
};

var gen = function* (){
  var f1 = yield readFile('/etc/fstab');
  var f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

然后，手动执行上面的 Generator 函数。

```javascript
var g = gen();
g.next().value.then(function(data) {
  g.next(data).value.then(function(data) {
    g.next(data);
  });
});
```

手动执行其实就是用 `then` 方法，层层添加回调函数。理解了这一点，就可以写出一个自动执行器。

```javascript
function run(gen){
  var g = gen();
  function next(data){
    var result = g.next(data);
    if (result.done) return result.value;
    result.value.then(function(data){
      next(data);
    });
  }

  next();
}
run(gen);
```

上面代码中，只要 Generator 函数还没执行到最后一步，`next` 函数就调用自身，以此实现自动执行。

#### 5.4 co 模块的源码

co 就是上面那个自动执行器的扩展。

1. 首先，co 函数接受 Generator 函数作为参数，返回一个 Promise 对象。

    ```javascript
    function co(gen) {
      var ctx = this;
      return new Promise(function(resolve, reject) {});
    }
    ```

    在返回的 Promise 对象里面，co 先检查参数 `gen` 是否为 Generator 函数。如果是，就执行该函数，得到一个内部指针对象；如果不是就返回，并将 Promise 对象的状态改为 `resolved`。

    ```javascript
    function co(gen) {
      var ctx = this;
      return new Promise(function(resolve, reject) {
        if (typeof gen === 'function') gen = gen.call(ctx);
        if (!gen || typeof gen.next !== 'function') return resolve(gen);
      });
    }
    ```

2. 接着，co 将 Generator 函数的内部指针对象的 `next` 方法，包装成 `onFulfilled` 函数。这主要是为了能够捕捉抛出的错误。

    ```javascript
    function co(gen) {
      var ctx = this;

      return new Promise(function(resolve, reject) {
        if (typeof gen === 'function') gen = gen.call(ctx);
        if (!gen || typeof gen.next !== 'function') return resolve(gen);

        onFulfilled();
        function onFulfilled(res) {
          var ret;
          try {
            ret = gen.next(res);
          } catch (e) {
            return reject(e);
          }
          next(ret);
        }
      });
    }
    ```

3. 最后，就是关键的 `next` 函数，它会反复调用自身。

    ```javascript
    function next(ret) {
      if (ret.done) return resolve(ret.value);
      var value = toPromise.call(ctx, ret.value);
      if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
      return onRejected(
        new TypeError(
          'You may only yield a function, promise, generator, array, or object, '
          + 'but the following object was passed: "'
          + String(ret.value)
          + '"'
        )
      );
    }
    ```

    上面代码中，`next` 函数的内部代码，一共只有四行命令。

    - 第一行，检查当前是否为 Generator 函数的最后一步，如果是就返回。
    - 第二行，确保每一步的返回值，是 Promise 对象。
    - 第三行，使用 `then` 方法，为返回值加上回调函数，然后通过 `onFulfilled` 函数再次调用 `next` 函数。
    - 第四行，在参数不符合要求的情况下（参数非 Thunk 函数和 Promise 对象），将 Promise 对象的状态改为 `rejected`，从而终止执行。

#### 5.5 处理并发的异步操作

co 支持并发的异步操作，即允许某些操作同时进行，等到它们全部完成，才进行下一步。这时，要把并发的操作都放在数组或对象里面，跟在 `yield` 语句后面。

```javascript
// 数组的写法
co(function* () {
  var res = yield [
    Promise.resolve(1),
    Promise.resolve(2)
  ];
  console.log(res);
}).catch(onerror);

// 对象的写法
co(function* () {
  var res = yield {
    1: Promise.resolve(1),
    2: Promise.resolve(2),
  };
  console.log(res);
}).catch(onerror);
```

下面是另一个例子。

```javascript
co(function* () {
  var values = [n1, n2, n3];
  yield values.map(somethingAsync);
});

function* somethingAsync(x) {
  // do something async
  return y
}
```

上面的代码允许并发三个 `somethingAsync` 异步操作，等到它们全部完成，才会进行下一步。

此外，[这里](https://es6.ruanyifeng.com/#docs/generator-async#%E5%AE%9E%E4%BE%8B%EF%BC%9A%E5%A4%84%E7%90%86-Stream)还有一个在 Node 中处理 Stream 的例子可以参考。

## 十三、参考

- 阮一峰，[Generator 函数的语法](https://es6.ruanyifeng.com/#docs/generator)
- 阮一峰，[Generator 函数的异步应用](https://es6.ruanyifeng.com/#docs/generator-async)
