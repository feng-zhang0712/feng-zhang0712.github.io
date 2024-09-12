# requestIdleCallback

## 一、概念

window 对象的 `requestIdleCallback` 是一种浏览器 API，允许开发者在浏览器的空闲时间执行低优先级的任务，而不会影响用户的交互体验。它的设计目的是利用浏览器的空闲时间来执行非紧急的任务，从而提高应用程序的性能和响应速度。

## 二、用法

### 2.1 基本用法

`requestIdleCallback` 方法接受一个回调函数，这个回调函数会在浏览器空闲时被调用。它还可以接受一个可选的配置对象，该对象可以指定超时时间。如果在指定的时间内浏览器没有空闲时间，回调函数将被强制执行。

```javascript
const handle = requestIdleCallback(callback[, options])
```

- `callback`：当浏览器空闲时要执行的函数。这个函数会接收到一个 `IdleDeadline` 对象作为参数。这个对象有两个主要的方法和属性：
  - `timeRemaining()`：返回一个浮点数，表示浏览器当前帧内剩余的空闲时间（以毫秒为单位）。如果返回值为零，表示没有剩余的空闲时间。
  - `didTimeout`：一个布尔值，表示回调函数是否因为 `timeout` 到期而被强制执行。
- `options`（可选）：一个配置对象，可以包含以下属性：
  - `timeout`：一个整数，表示在多少毫秒后强制执行回调。
- 返回值：一个 ID，可以把它传入 `cancelIdleCallback()` 方法来结束回调。

```javascript
function myIdleCallback(deadline) {
  while (deadline.timeRemaining() > 0 && tasks.length > 0) {
    doWorkIfNeeded();
  }
  if (tasks.length > 0) {
    requestIdleCallback(myIdleCallback);
  }
}

requestIdleCallback(myIdleCallback);
```

在上面的示例中，`myIdleCallback` 会在浏览器的空闲时间被调用。`deadline.timeRemaining()` 返回当前浏览器空闲时间的剩余毫秒数。如果任务队列 `tasks` 还有剩余任务，但 `deadline.timeRemaining()` 已经为零，则会重新调度 `requestIdleCallback`。

### 2.2 将 `requestIdleCallback` 与 `setTimeout` 结合使用

为了兼容不支持 `requestIdleCallback` 的浏览器，可以使用 `setTimeout` 作为降级处理：

```javascript
function myIdleCallback(deadline) {
  while (deadline.timeRemaining() > 0 && tasks.length > 0) {
    doWorkIfNeeded();
  }
  if (tasks.length > 0) {
    requestIdleCallback(myIdleCallback);
  }
}

function requestIdleCallbackPolyfill(cb) {
  if ('requestIdleCallback' in window) {
    return requestIdleCallback(cb);
  } else {
    return setTimeout(() => {
      const start = Date.now();
      cb({
        timeRemaining: () => Math.max(0, 50 - (Date.now() - start)),
        didTimeout: false
      });
    }, 1);
  }
}

requestIdleCallbackPolyfill(myIdleCallback);
```

在上面的示例中，`requestIdleCallbackPolyfill` 函数检查浏览器是否支持 `requestIdleCallback`，如果不支持，则使用 `setTimeout` 模拟类似的行为。

## 三、取消回调方法的执行

window 对象提供了 `cancelIdleCallback()` 方法用于取消之前调用 `requestIdleCallback()` 的回调。只需将 `requestIdleCallback()` 方法返回的ID传递给 `cancelIdleCallback()` 方法即可。

```javascript
cancelIdleCallback(handle)
```

## 四、使用场景

`requestIdleCallback` 适用于以下场景：

1. **非关键任务**：例如分析数据、发送分析报告、缓存处理、预加载资源等，这些任务不需要立即完成，可以在浏览器空闲时间执行。
2. **性能优化**：例如在用户交互和动画完成后执行不重要的操作，避免阻塞主线程，确保应用程序的流畅性。
3. **渐进式增强**：在浏览器性能较好时执行一些增强功能，例如在页面加载完成后添加不重要的动画效果。

## 五、注意事项

- **兼容性**：`requestIdleCallback` 并不是所有浏览器都支持，特别是一些旧版本的浏览器。在使用时需要考虑兼容性问题，可以使用 `setTimeout` 作为降级处理。
- **任务拆分**：将大任务拆分为多个小任务，以便充分利用浏览器的短暂空闲时间。
- **优先级管理**：合理安排任务优先级，确保重要任务优先执行。
