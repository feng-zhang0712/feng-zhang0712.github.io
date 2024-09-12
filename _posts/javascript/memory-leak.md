# 内存泄漏

## 一、概念

内存泄漏（Memory Leak）是指程序在运行过程中，未能及时释放不再使用的内存，导致内存占用不断增加，最终可能导致系统内存耗尽或程序崩溃。

## 二、垃圾回收机制

浏览器中的垃圾回收机制（Garbage Collection，简称 GC）的任务是识别和回收不再被引用的内存。

垃圾回收机制通常基于两个主要算法：**引用计数算法（Reference Counting）** 和 **标记-清除算法（Mark-and-Sweep）**。

### 2.1 引用计数算法

引用计数算法是一种较早的垃圾回收算法，它维护每个对象的引用计数，当引用计数变为零时，该对象的内存就可以被回收。

引用计数算法的缺点是，它无法处理循环引用问题。比如，当两个对象互相引用但不再被其他对象引用时，它们的计数不会变为零，从而导致内存泄漏。

```javascript
function createCycle() {
  const obj1 = {};
  const obj2 = {};

  obj1.next = obj2; // obj1 引用 obj2
  obj2.prev = obj1; // obj2 引用 obj1

  return 'Cycle created'; // 由于 obj1 和 obj2 互相引用，它们的引用计数不会变为零
}

createCycle(); // 虽然 createCycle 函数退出，但 obj1 和 obj2 无法被回收
```

现代浏览器通常不会单独使用引用计数算法，而是结合标记-清除算法来处理循环引用问题。

### 2.2 标记-清除算法

标记-清除算法是现代 JavaScript 引擎中最常用的垃圾回收算法。它的工作流程如下。

- **标记阶段**：垃圾回收器定期从根对象（全局对象或局部作用域）开始，标记或寻找所有可以获得的对象和收集所有不能获得的对象。
- **清除阶段**：遍历所有对象，回收那些没有被标记的对象（即那些不可到达的对象）的内存。

```javascript
let obj1 = { a: 1 };
let obj2 = { b: 2 };

obj1.next = obj2; // obj1 引用 obj2
obj2.prev = obj1; // obj2 引用 obj1

obj1 = null; // 断开 obj1 的引用

// 垃圾回收器执行时，会发现 obj1 不可到达，而 obj2 仍然可到达（通过全局对象的引用）
```

### 2.3 垃圾回收的触发条件

垃圾回收器并不会在每一时刻都运行，它会在以下几种情况下被触发。

- **内存分配达到一定阈值**：当分配的内存达到预定阈值时，垃圾回收器会启动。
- **内存申请失败**：当尝试分配新内存但失败时，垃圾回收器可能会被触发以释放内存。
- **闲时触发**：一些垃圾回收器会在浏览器空闲时主动运行，以保持内存的健康状态。

### 2.4 垃圾回收的优化策略

现代浏览器的JavaScript引擎实现了多个优化策略来提高垃圾回收的效率：

- **增量收集（Incremental Garbage Collection）**：将垃圾回收过程分成多个小步骤，避免长时间的卡顿。
- **分代收集（Generational Garbage Collection）**：将内存分为不同的代（如新生代和老生代），新生成的对象放在新生代，存活较长时间的对象移动到老生代。新生代中的对象通常很快会被回收。
- **并行收集（Parallel Garbage Collection）**：使用多线程并行执行垃圾回收任务，提高性能。

## 三、内存泄漏的原因

### 3.1 意外的全局变量

未正确声明的变量会成为全局变量，并一直存在于内存中，无法被垃圾回收机制回收。

```javascript
function createGlobalVariable() {
  globalVar = 'I am global'; // 未使用 var、let、const 声明，意外创建全局变量
}

createGlobalVariable();
console.log(globalVar); // 'I am global'
```

### 3.2 计时器或事件监听没有及时清除

未清理的计时器（如 `setInterval`、`setTimeout`）或事件监听器会持续占用内存。

```javascript
function startTimer() {
  setInterval(() => {
    console.log('This will run forever');
  }, 1000);
}

startTimer();
```

### 3.3 闭包（Closure）中的引用

在闭包中，嵌套函数会保留对外部函数作用域的引用，可能导致未使用的内存无法被回收。

```javascript
function createClosure() {
  const largeObject = { /* large data */ };

  return function() {
    console.log(largeObject);
  };
}

const closure = createClosure();
// largeObject 将一直保存在内存中，直到 closure 被释放
```

### 3.4 DOM 引用

JavaScript 对象和 DOM 元素之间的循环引用会导致内存无法被回收。

```javascript
const element = document.getElementById('myElement');
const obj = {
  elem: element
};

element = null; // DOM 元素仍然被 obj 引用，无法被回收
```

## 四、内存泄漏的检测

### 4.1 浏览器开发者工具

现代浏览器（如 Chrome、Firefox）提供了开发者工具，可以用来检测和分析内存泄漏。

#### （1）堆快照（Heap Snapshot）

堆快照可用于查看内存中的对象和引用关系，分析内存泄漏的原因。

- 打开 Chrome 开发者工具，切换到 “Memory” 面板。
- 点击 “Take heap snapshot” 按钮，生成当前内存快照。
- 对比多次快照，找出内存持续增加的对象。

#### （2）时间线记录（Timeline Recording）

时间线记录可以帮助分析内存使用情况的变化趋势。

- 打开 Chrome 开发者工具，切换到 “Performance” 面板。
- 点击 “Start profiling and reload page” 按钮，记录页面加载和运行过程中的内存使用情况。

### 4.2 第三方工具和库

一些第三方工具和库可以帮助检测和分析内存泄漏，如 `LeakCanary`（Android）和 `MemLab`（Node.js）。

## 参考

- [「硬核JS」你真的了解垃圾回收机制吗](https://juejin.cn/post/6981588276356317214)
