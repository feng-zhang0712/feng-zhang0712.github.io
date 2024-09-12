# Promise.prototype.finally()

## Promise 的 `finally` 方法

在 JavaScript 中，`Promise.prototype.finally()` 方法是 ES2018 引入的一种方法，用于在 `Promise` 操作完成后，无论其是否成功或失败，都会执行指定的回调函数。这使得我们能够在 Promise 结束时执行一些清理工作，而无需在 `then` 和 `catch` 方法中重复代码。

```javascript
promise.finally(onFinally)
```

- `onFinally` 是一个无参数的函数，当 Promise 完成（即变为 settled 状态，无论是 resolved 还是 rejected）时执行。

```javascript
new Promise((resolve, reject) => {
  // 模拟异步操作
  setTimeout(() => resolve('Success'), 1000);
})
  .then((result) => {
    console.log(result); // 输出: Success
  })
  .catch((error) => {
    console.error(error);
  })
  .finally(() => {
    console.log('Cleanup'); // 始终会执行
  });
```

在上面的例子中，无论 Promise 是被 resolve 还是 reject，`finally` 回调都会被调用。

## 手动实现 `finally` 方法

要手动实现一个 `finally` 方法，我们可以通过扩展 `Promise.prototype` 来实现。核心思想是使用 `then` 和 `catch` 方法来确保 `finally` 回调无论在什么情况下都会被执行。

```javascript
if (!Promise.prototype.finally) {
  Promise.prototype.finally = function (callback) {
    // 获取当前的 Promise 对象
    const promise = this;

    // 定义一个包装回调函数，用于保证 callback 无论如何都会被执行
    const onFinally = () => Promise.resolve(callback());

    // 使用 then 和 catch 方法来确保 onFinally 回调被执行
    return promise.then(
      (result) => onFinally().then(() => result), // 确保 resolve 的值被正确传递
      (error) => onFinally().then(() => { throw error; }) // 确保 reject 的错误被正确传递
    );
  };
}

// 测试自定义的 finally 方法
new Promise((resolve, reject) => {
  // 模拟异步操作
  setTimeout(() => resolve('Success'), 1000);
})
  .then((result) => {
    console.log(result); // 输出: Success
  })
  .catch((error) => {
    console.error(error);
  })
  .finally(() => {
    console.log('Cleanup'); // 始终会执行
  });
```

在上面的实现中，我们首先检查 `Promise.prototype` 上是否已经存在 `finally` 方法，如果不存在，则添加我们的自定义实现。

- `onFinally` 回调会使用 `Promise.resolve` 包装，以确保无论 `callback` 是同步还是异步操作，其结果都可以被正确处理。
- 在 `then` 方法中，我们传递了两个回调函数：一个用于处理成功的情况，另一个用于处理失败的情况。
- 每个回调函数在执行完 `onFinally` 回调后，确保原始的结果或错误被正确传递。

这种实现方式确保了 `finally` 回调在 `Promise` 结束时被执行，并且不会影响 `Promise` 原本的行为。
