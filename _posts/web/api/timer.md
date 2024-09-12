# setTimeout 和 setInterval

## 一、事件循环（Event Loop）和任务队列（Task Queue）

JavaScript 是单线程的，这意味着它一次只能执行一个任务。事件循环和任务队列是管理和调度任务执行的关键机制。（JavaScript 只在一个线程上运行，不代表 JavaScript 引擎只有一个线程。事实上，JavaScript 引擎有多个线程，单个脚本只能在一个线程上运行（称为主线程），其他线程都是在后台配合。）

### 1.1 调用栈（Call Stack）

调用栈（Call Stack）是 JavaScript 引擎用来管理函数调用的一个数据结构，保存着所有正在执行的函数。它遵循后进先出（LIFO, Last In First Out）的原则，当一个函数被调用时，它会被压入栈顶；当函数执行完毕时，它会从栈顶弹出。

### 1.2 任务队列（Task Queue）

任务队列是用于存放异步任务的回调函数（如通过 `setTimeout` 设置的回调函数）的队列。任务队列中的任务按照先进先出（FIFO, First In First Out）的顺序执行，也就是说，先进入任务队列中的任务，会优先被取出。当调用 `setTimeout` 时，指定的回调函数会被添加到任务队列中。

任务队列中的任务，分为宏任务（任务）和微任务。

- **宏任务（Macro Task）**：包括 `setTimeout`、`setInterval`、`setImmediate`、I/O 任务、UI 渲染等。
- **微任务（Micro Task）**：包括 Promise 的 `.then()` 回调、`process.nextTick`（Node.js 特有）、MutationObserver 回调等。

### 1.3 事件循环（Event Loop）

事件循环是一个不断检查调用栈和任务队列的循环机制。当调用栈为空时，事件循环会从任务队列中取出第一个任务并将其压入调用栈，开始执行。事件循环会重复上述过程，直到没有任务需要处理。

事件循环的工作原理如下：

1. 检查执行栈是否为空。
2. 如果执行栈为空，从微任务队列中取出所有的微任务并执行，直到微任务队列为空。
3. 从宏任务队列中取出第一个宏任务，将其回调函数添加到执行栈中执行。
4. 重复上述过程。

```javascript
console.log('Start');

setTimeout(() => {
  console.log('Timeout');
}, 0);

Promise.resolve().then(() => {
  console.log('Promise');
});

console.log('End'); // 输入顺序：Start、End、Promise、Timeout。
```

上面代码的执行过程如下：

首先执行同步代码。

- `console.log('Start')` 输出 Start。
- `setTimeout` 注册回调，回调被添加到宏任务队列中。
- `Promise.resolve().then` 注册回调，回调被添加到微任务队列中。
- `console.log('End')` 输出 End。
  
执行栈为空后，事件循环开始工作。

- 检查微任务队列，发现有一个微任务（Promise 的 `.then()` 回调），执行这个回调，输出 Promise。
- 微任务队列为空后，检查宏任务队列，发现有一个宏任务（`setTimeout` 的回调），执行这个回调，输出 Timeout。

以下，是一个更复杂的例子。

```javascript
console.log('Start');

setTimeout(() => {
  console.log('Timeout 1');
  Promise.resolve().then(() => {
    console.log('Promise 1');
  });
}, 0);

setTimeout(() => {
  console.log('Timeout 2');
}, 0);

Promise.resolve().then(() => {
  console.log('Promise 2');
});

console.log('End'); // 输出顺序：Start、End、Promise 2、Timeout 1、Promise 1、Timeout 2
```

上面代码的执行过程如下：

首先执行同步代码。

- `console.log('Start')` 输出 Start。
- `setTimeout` 注册两个回调，回调被添加到宏任务队列中。
- `Promise.resolve().then` 注册一个回调，回调被添加到微任务队列中。
- `console.log('End')` 输出 End。

执行栈为空后，事件循环开始工作。

- 检查微任务队列，发现有一个微任务（Promise 2 的回调），执行这个回调，输出 Promise 2。
- 微任务队列为空后，检查宏任务队列：
  - 取出第一个宏任务 Timeout 1 的回调，执行这个回调，输出 Timeout 1。
  - 在 Timeout 1 的回调中，注册了一个新的微任务 Promise 1 的回调，回调被添加到微任务队列中。
  - 检查微任务队列，发现有一个微任务（Promise 1 的回调），执行这个回调，输出 Promise 1。
- 检查宏任务队列，取出第二个宏任务 Timeout 2 的回调，执行这个回调，输出 Timeout 2。

## 二、setTimeout

### 2.1 介绍

`setTimeout` 方法用于在一定的时间后执行一个回调函数或指定的代码片段。

```javascript
const timerId = setTimeout(function, delay, [param1, param2, ...]);
```

`setTimeout` 方法接受三个参数。

- `function`：要执行的回调函数，此参数可以是匿名函数、具名函数或者字符串（比如：`"console.log('Hello World!');"`）。
- `delay`（可选）：延迟时间（以毫秒为单位），如果没有显示指定，delay 默认为 0ms。如果是字符串，会被转换为数值。
- `[param1, param2, ...]`（可选）：传递给回调函数的参数。

`setTimeout` 方法的返回值是一个整数，可以通过调用 `clearTimeout` 方法来取消该定时器。

```javascript
clearTimeout(timerId);
```

### 2.2 运行机制

`setTimeout` 的核心机制可以分为以下几个步骤。

1. **调用 `setTimeout`**：当调用 `setTimeout` 时，浏览器或 Node.js 环境会将回调函数和计时器（定时器）一同注册。
2. **计时器开始计时**：计时器开始计时，计时器会在 **指定的延迟时间后** 将回调函数添加到任务队列中。
3. **事件循环（Event Loop）**：JavaScript 运行时环境（如浏览器或 Node.js）会不断运行事件循环，以处理任务队列中的任务。
4. **任务队列（Task Queue）**：一旦计时器到达指定的延迟时间，回调函数将被添加到任务队列中，等待事件循环的执行。
5. **执行回调函数**：当事件循环处理到任务队列中的回调函数时，回调函数将被执行。

从上面可以看出，`setTimeout` 并非在指定的时间之后执行回调函数，而是在指定的时间之后，将回调函数放入任务队列。如果此时当前线程有空闲时间，并且任务队列为空，则回调函数会被立即执行，否则，回调函数将会延迟执行。

### 2.3 setTimeout(callback, 0)

通过设置 `setTimeout(callback, 0)`（或者 `setTimeout(callback)`），并不会让 `callback` 函数立即执行，但是，这样可以让回调函数尽快执行。也就是说，`setTimeout(callback, 0)` 会在下一轮事件循环一开始就执行。

## 三、setInterval

### 3.1 介绍

`setInterval` 方法用于按照固定的时间间隔重复调用一个函数。它的返回值是一个整数，可以通过调用 `clearInterval` 方法来取消该定时器。`setInterval` 的使用方式同 `setTimeout` 类似。

### 3.2 运行机制

`setInterval` 的核心机制可以分为以下几个步骤。

1. **调用 `setInterval`**：当调用 `setInterval` 时，浏览器或 Node.js 环境会将回调函数和计时器（定时器）一同注册。
2. **计时器开始计时**：计时器开始计时，在达到指定的时间间隔（delay）后，将回调函数添加到任务队列中。
3. **重复添加任务**：每次达到时间间隔后，计时器都会将回调函数添加到任务队列中，直到调用 `clearInterval` 明确停止。
4. **事件循环（Event Loop）**：JavaScript 运行时环境（如浏览器或 Node.js）会不断运行事件循环，以处理任务队列中的任务。
5. **任务队列（Task Queue）**：每次计时器到达指定的时间间隔，回调函数将被添加到任务队列中，等待事件循环的执行。
6. **执行回调函数**：当事件循环处理到任务队列中的回调函数时，回调函数将被执行。

从上面可以看出，`setInterval` 的执行机制，同 `setInterval` 类似，不同之处在于，`setInterval` 的回调函数，会被按固定时间间隔添加到任务队列中。回调函数具体的执行时机，也是一个不确定的状态。

以下是一个，`setInterval` 与事件循环的例子。

```javascript
console.log('Start');

const intervalId = setInterval(() => {
  console.log('Interval');
}, 1000);

setTimeout(() => {
  console.log('Timeout');
  clearInterval(intervalId); // 停止interval
}, 3500);

console.log('End'); // 输出顺序：Start、End、Interval、Interval、Interval、Timeout
```

上面代码的执行过程如下：

- 首先执行 `console.log('Start')`，输出 Start。
- 调用 `setInterval`，每秒将回调函数添加到任务队列。
- 调用 `setTimeout`，注册一个3.5秒后的回调函数。
- 执行 `console.log('End')`，输出 End。
- 每秒执行一次 Interval 回调函数，输出 Interval。
- 3.5秒后，执行 Timeout 回调函数，输出 Timeout，并停止 setInterval。

需要注意的是，由于 JavaScript 是单线程的，如果某个回调函数的执行时间超过了时间间隔，则后续的回调函数将会被排队等待。

## 四、setInterval 的问题

### 4.1 无法确保严格的时间间隔

#### （1）回调函数执行时间小于时间间隔

由于 `setInterval` 会在固定的时间间隔之后将回调函数添加到任务队列，如果此时任务队列正好为空，并且系统处于空闲状态，那么回调函数会被立即执行，考虑到回调函数的执行时间，两次回调函数之间的时间间隔，很可能小于我们设定的时间。举例来说，我们设定 `setInterval` 的时间间隔为100ms，在这种情况下，从上次回调函数执行完毕，到下次回调函数开始执行，中间的时间间隔可能只有10ms。

解决办法也很简单，我们可以通过使用嵌套的 `setTimeout` 来确保，在指定的时间间隔后执行回调函数。

```javascript
let timerId = setTimeout(function callback() {
  // ...
  timerId = setTimeout(callback, 1000);
}, 2000);
```

上面代码可以确保，下一次执行总是在本次执行结束之后的1000ms后开始。

#### （2）回调函数执行时间超过时间间隔

如果回调函数的执行时间超过设定的时间间隔，`setInterval` 会继续按指定时间间隔向任务队列中添加回调函数，导致回调函数积压。这会影响性能，并增加内存消耗。

```javascript
setInterval(() => {
  console.log('Interval Start');
  // 模拟长时间执行的回调函数
  const start = Date.now();
  while (Date.now() - start < 1500) {}
  console.log('Interval End');
}, 1000);

// 输出：Interval Start、Interval End、Interval Start、Interval End
```

对于这种情况，可以通过嵌套调用 `setTimeout` 来模拟 `setInterval` 的行为，从而避免回调函数积压和时间间隔不准确的问题。

```javascript
function repeatFunction() {
  console.log('Timeout Start');
  // 模拟长时间执行的回调函数
  const start = Date.now();
  while (Date.now() - start < 1500) {}
  console.log('Timeout End');

  // 确保回调函数执行完毕后再启动新的计时
  setTimeout(repeatFunction, 1000);
}

setTimeout(repeatFunction, 1000);
```

上面的代码，每次回调函数执行完毕后，才启动新的计时，避免了回调积压。

#### （3）动态调整时间间隔

通过记录每次回调函数的执行时间，并动态调整下一次调用的时间间隔，以确保尽可能精确的时间间隔。

```javascript
let expectedTime = Date.now() + 1000;

function repeatFunction() {
  const currentTime = Date.now();
  const drift = currentTime - expectedTime;

  console.log('Interval Start');
  // 模拟长时间执行的回调函数
  const start = Date.now();
  while (Date.now() - start < 1500) {}
  console.log('Interval End');

  expectedTime += 1000;

  setTimeout(repeatFunction, Math.max(0, 1000 - drift));
}

setTimeout(repeatFunction, 1000);
```

以上的代码，通过计算时间漂移（drift），动态调整下一次调用的时间间隔，以保持较为准确的定时。

### 4.2 回调函数错误处理

如果 `setInterval` 回调函数执行过程中抛出错误，后续的定时任务可能仍会继续执行，不容易捕获和处理错误。

对于这种情况，可以在 `setInterval` 回调函数中添加错误捕获机制，确保错误不会影响后续的定时任务执行。

```javascript
function repeatFunction() {
  try {
    console.log('Interval Start');
    // 模拟可能抛出错误的代码
    if (Math.random() < 0.3) {
      throw new Error('Random Error');
    }
    console.log('Interval End');
  } catch (error) {
    console.error('Error caught:', error);
  }
}

// 定期调用回调函数，并确保错误不会中断后续调用
const intervalId = setInterval(repeatFunction, 1000);
```

上面的代码，通过 `try...catch` 捕获并处理错误，确保错误不会影响后续的定时任务。

## 五、应用场景

### 5.1 setTimeout 应用场景

#### （1）延迟执行代码

`setTimeout` 最常见的应用场景是延迟执行代码。在指定的时间后执行某个函数，用于实现一些延迟效果或延迟加载。

```javascript
// 延迟2秒后显示提示信息
setTimeout(() => {
  alert('Hello after 2 seconds!');
}, 2000);
```

#### （2）模拟异步行为

在开发中，有时需要模拟异步行为（例如 API 请求或数据库查询）的延迟响应。

```javascript
function mockApiRequest(callback) {
  setTimeout(() => {
    const data = { userId: 1, userName: 'John Doe' };
    callback(data);
  }, 1000);
}

mockApiRequest((data) => {
  console.log('Received data:', data);
});
```

#### （3）防抖（Debouncing）和节流（Throttling）

防抖（Debouncing）和节流（Throttling）是前端性能优化中常用的技术，特别是在处理高频率触发的事件时。它们可以有效地减少函数调用次数，提升应用的性能和响应速度。具体的解释，可以参考 [防抖和节流](../../react/optimization/debounce-and-throttle.md) 部分。

#### （4）动画效果

在实现一些简单的动画效果时，可以使用 `setTimeout` 来控制动画帧的时间间隔。

```javascript
function animateElement(element) {
  let position = 0;
  function frame() {
    position++;
    element.style.left = position + 'px';
    if (position < 100) {
      setTimeout(frame, 10);
    }
  }
  setTimeout(frame, 10);
}

const element = document.getElementById('myElement');
animateElement(element);
```

除此之外，还可以考虑使用 [requestAnimationFrame](../windodw/request-animation-frame.md)。

### 5.2 setInterval 的应用场景

#### （1）定时轮询

`setInterval` 常用于定时轮询某个状态或数据，例如定时检查服务器状态或轮询获取最新数据。

```javascript
const intervalId = setInterval(() => {
  console.log('Checking server status...');
  // 模拟检查服务器状态
}, 5000);

// 停止轮询
setTimeout(() => {
  clearInterval(intervalId);
  console.log('Stopped checking server status');
}, 20000);
```

#### （2）实现定时任务

`setInterval` 可以用来实现定时任务，例如每隔一段时间执行某个操作。

```javascript
const intervalId = setInterval(() => {
  console.log('Executing periodic task');
}, 3000);

// 停止定时任务
setTimeout(() => {
  clearInterval(intervalId);
  console.log('Stopped periodic task');
}, 15000);
```

#### （3）时钟或计时器

`setInterval` 常用于实现时钟或计时器，每秒更新一次显示。

```javascript
function startClock() {
  const clockElement = document.getElementById('clock');
  setInterval(() => {
    const now = new Date();
    clockElement.textContent = now.toLocaleTimeString();
  }, 1000);
}

startClock();
```

#### （4）动画循环

`setInterval` 可以用来实现简单的动画循环效果，在固定时间间隔内更新动画状态。

```javascript
function animateElement(element) {
  let position = 0;
  const intervalId = setInterval(() => {
    position++;
    element.style.left = position + 'px';
    if (position >= 100) {
      clearInterval(intervalId);
    }
  }, 10);
}

const element = document.getElementById('myElement');
animateElement(element);
```

总之，对于 `setInterval` 来说，除了一些特殊的场景，开发中实际使用的并不多。如果可以使用 `setTimeout` 或者其他更好的方案，应该优先考虑使用它们。

## 六、setTimeout 和 setInterval 的比较

相同点：

- **基本语法相似**；
- **异步执行**：两者都是异步操作，回调函数不会立即执行，而是被添加到任务队列中，等待事件循环的调度执行。
- **事件循环和任务队列**：两者都依赖事件循环和任务队列来调度和执行回调函数。

不同点：

- 执行次数：`setTimeout` 只执行一次回调函数；`setInterval` 会按指定的时间间隔重复执行回调函数，直到被取消。
- 内部机制：`setTimeout` 计时器到期后，将回调函数添加到任务队列中；`setInterval` 每次到达时间间隔后，都会将回调函数添加到任务队列中，因此如果回调函数执行时间较长，可能会导致回调函数的执行频率低于预期。（或者说，`setTimeout` 可以保证函数在指定的时间间隔内不会执行，而 `setInterval` 无法保证）

参考

- [setTimeout() 全局函数](https://developer.mozilla.org/zh-CN/docs/Web/API/setTimeout)
- [调度：setTimeout 和 setInterval](https://zh.javascript.info/settimeout-setinterval)
- [事件循环：微任务和宏任务](https://zh.javascript.info/event-loop)
- [定时器](https://wangdoc.com/javascript/async/timer)
- [js 基础之 setTimeout 与 setInterval 原理分析](https://developer.jdcloud.com/article/2616)
