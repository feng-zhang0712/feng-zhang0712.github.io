# 网页生命周期（Page Lifecycle）

网页生命周期（Page Lifecycle）是指一个网页从加载到卸载的整个过程，包括若干个不同的事件和状态转变。

## 一、解析 HTML 和构建 DOM 树

在此阶段，浏览器开始解析 HTML 文件，将其转换成 DOM 树。

## 二、`DOMContentLoaded` 事件

当 HTML 文档完全解析，且所有延迟脚本（`<script defer src="…">` 和 `<script type="module">`）下载和执行完毕后，会触发 `DOMContentLoaded` 事件。它不会等待图片、子框架和异步脚本等其他内容完成加载。这个事件比 `load` 事件，发生时间早得多。

```javascript
document.addEventListener('DOMContentLoaded', () => {
  console.log('DOM fully loaded and parsed');
  // 可以在这里进行 DOM 操作或初始化
});
```

当 Document 对象和 XMLHttpRequest 对象的 `readyState` 属性发生变化时会触发 `readystatechange` 事件。`document.readyState` 有三个可能的值：

- `loading`：表示网页正在加载
- `interactive`：表示网页已经解析完成，但是外部资源仍然处在加载状态
- `complete`：表示网页和所有外部资源已经结束加载，`load` 事件即将触发

## 三、加载外部资源（CSS、JavaScript、图片等）

在此阶段，浏览器开始下载和解析外部资源，如 CSS 样式表、JavaScript 文件和图片等。

## 四、`load` 事件

`load` 事件在整个页面及所有依赖资源如样式表和图片都已完成加载时触发。该事件不可取消，也不会冒泡。如果页面或资源从浏览器缓存加载，则不会触发 `load` 事件。

  ```javascript
  window.addEventListener('load', () => {
    console.log('Page fully loaded');
    // 可以在这里进行与所有资源加载完毕相关的操作
  });
  ```

## 五、`beforeunload` 事件

当浏览器窗口将要关闭或者刷新时，会触发 `beforeunload` 事件。

```javascript
window.addEventListener('beforeunload', (event) => {
  event.preventDefault();
  event.returnValue = ''; // 现代浏览器需要设置 returnValue 以显示确认对话框
  return ''; // 有些旧浏览器需要返回一个字符串来显示确认对话框
});
```

注意：现代浏览器不允许自定义提示消息，只会显示默认的提示。此外，浏览器对这个事件的行为很不一致，有的浏览器调用 `event.preventDefault()`，也会弹出对话框。

## 六、`unload` 事件

`unload` 事件在窗口、文档及其资源卸载之后触发。此事件通常用于执行清理操作，例如取消未完成的网络请求或清除临时数据。它的触发顺序排在 `beforeunload`、`pagehide` 事件后面。

`unload` 事件发生时，文档处于一个特殊状态。所有资源依然存在，但是对用户来说都不可见，UI 互动全部无效。这个事件是无法取消的，即使在监听函数里面抛出错误，也不能停止文档的卸载。

```javascript
window.addEventListener('unload', () => {
  console.log('Page is being unloaded');
  // 可以在这里进行页面卸载前的清理操作
});
```

## 七、其他事件

### 6.1 `pageshow` 事件

默认情况下，浏览器会在当前会话（session）缓存页面，当用户点击“前进/后退”按钮时，浏览器就会从缓存中加载页面。

`pageshow` 事件在页面加载时触发，包括第一次加载和从缓存加载两种情况。如果要指定页面每次加载（不管是不是从浏览器缓存）时都运行的代码，可以放在这个事件的监听函数。

第一次加载时，它的触发顺序排在 `load` 事件后面。从缓存加载时，`load` 事件不会触发，因为网页在缓存中的样子通常是 `load` 事件的监听函数运行后的样子，所以不必重复执行。同理，如果是从缓存中加载页面，网页内初始化的 JavaScript 脚本（比如 `DOMContentLoaded` 事件的监听函数）也不会执行。

`pageshow` 事件有一个 `persisted` 属性，返回一个布尔值。页面第一次加载时，这个属性是 `false`；当页面从缓存加载时，这个属性是 `true`。

### 6.2 `pagehide` 事件

`pagehide` 事件与 `pageshow` 事件类似，当用户通过“前进/后退”按钮，离开当前页面时触发。它与 `unload` 事件的区别在于，如果在 window 对象上定义 `unload` 事件的监听函数之后，页面不会保存在缓存中，而使用 `pagehide` 事件，页面会保存在缓存中。

`pagehide` 事件实例也有一个 `persisted` 属性，将这个属性设为 `true`，就表示页面要保存在缓存中；设为 `false`，表示网页不保存在缓存中，这时如果设置了 `unload` 事件的监听函数，该函数将在 `pagehide` 事件后立即运行。

注意，这两个事件只在浏览器的 history 对象发生变化时触发，跟网页是否可见没有关系。

### 6.3 `popstate` 事件

`popstate` 事件在浏览器的 history 对象的当前记录发生显式切换时触发。注意，调用 `history.pushState()` 或 `history.replaceState()`，并不会触发 `popstate` 事件。该事件只在用户在 history 记录之间显式切换时触发，比如鼠标点击“后退/前进”按钮，或者在脚本中调用 `history.back()`、`history.forward()`、`history.go()` 时触发。

### 6.4 `hashchange` 事件

`hashchange` 事件在 URL 的 hash 部分（即#号后面的部分，包括 `#` 号）发生变化时触发。该事件一般在 window 对象上监听。

`hashchange` 的事件实例具有两个特有属性：`oldURL` 属性和 `newURL` 属性，分别表示变化前后的完整 URL。

## 八、关于 `Navigator.sendBeacon(url, data)` 方法

`navigator.sendBeacon()` 方法用于通过 HTTP POST 将少量数据 异步 传输到 Web 服务器。它的使用场景，主要是有时用户卸载网页的时候，需要向服务器发一些数据，就可以使用此方法。

参数

- `url`：表示目标服务器的 URL
- `data`（可选）：表示要发送的数据，可以是任意类型（字符串、表单对象、二进制对象等等）

它的返回值是一个布尔值，成功发送数据为 `true`，否则为 `false`。

注意，应该避免在 `unload`、`beforeunload` 或 `pagehide` 事件中执行异步操作，因为这三个方法并不会保证一定能够执行。
