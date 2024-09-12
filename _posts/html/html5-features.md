# HTML 5 新特性

## 一、新的元素

### 1.1 新的语义化元素

HTML5 引入了许多新的语义元素，帮助开发者更清晰地定义文档结构，使搜索引擎和辅助技术更容易理解页面内容。

- `<header>`：定义页面或区域的页眉。
- `<footer>`：定义页面或区域的页脚。
- `<section>`：定义文档中的章节。
- `<article>`：定义独立的内容块，如博客文章或新闻条目。
- `<aside>`：定义与主内容相关的附加内容，如侧边栏。
- `<nav>`：定义导航链接区域。
- `<figure>` 和 `<figcaption>`：用于图像和图像说明。
- `<main>`：定义文档的主要内容。
- `<mark>`：定义高亮文本。

### 1.2 新的多媒体元素

- `<audio>`：用于嵌入音频文件。
- `<video>`：用于嵌入视频文件。

### 1.3 新的图形和绘图元素

- `<canvas>`：用于通过 JavaScript 绘制图形和动画。
- `<svg>`：用于定义可缩放矢量图形。

### 1.4 新的文档类型声明

简化的 DOCTYPE 声明：`<!DOCTYPE html>`

## 二、新的表单控件

HTML5 增强了表单控件，引入了新的输入类型和属性，简化了表单验证和交互。

- 新的输入类型：
  - `<input type="email">`：<自动验证>用于输入电子邮件地址。
  - `<input type="tel">`：<非自动验证>用于输入电话号码。
  - `<input type="url">`：<自动验证>用于输入 URL。
  - `<input type="date">`：<自动验证>用于输入日期。
  - `<input type="time">`：用于输入时间。
  - `<input type="number">`：用于输入数字。
  - `<input type="range">`：用于输入范围值（滑块）。
  - `<input type="color">`：用于选择颜色。
- 新的属性：`placeholder`、`required`、`pattern`、`autocomplete`、`autofocus` 等。

## 三、新的 JavaScript API

### 3.1 本地存储

HTML5 提供了多种本地存储机制，允许 Web 应用在客户端存储数据。

- `localStorage`：用于存储持久化数据，即使浏览器关闭后数据仍然存在。
- `sessionStorage`：用于存储会话数据，浏览器关闭后数据丢失。
- `IndexedDB`：用于存储更复杂的数据结构和大量数据。

### 3.2 Web Workers

Web Workers 允许在后台运行 JavaScript，避免阻塞主线程，提高应用性能。

```javascript
<!-- main.js -->
if (window.Worker) {
  const worker = new Worker('worker.js');
  worker.postMessage('Hello Worker');

  worker.onmessage = function(event) {
    console.log('Message from worker: ' + event.data);
  };
}

<!-- worker.js -->
onmessage = function(event) {
  postMessage('Hello from Worker');
};
```

### 3.3 Web Sockets

Web Sockets 提供了一种在客户端和服务器之间建立实时双向通信的机制。

### 3.4 WebGL

提供了在浏览器中进行 3D 渲染的功能。

### 3.5 地理位置

HTML5 提供了 Geolocation API，可以获取用户的地理位置信息。

此外，还有如 History API、File API 等。

## 四、拖放（Drag and Drop）

HTML5 增强了拖放功能，使其更容易实现拖放交互。

```html
<div id="drag1" draggable="true" ondragstart="drag(event)">Drag me</div>
<div id="dropZone" ondrop="drop(event)" ondragover="allowDrop(event)">Drop here</div>

<script>
  function allowDrop(event) {
    event.preventDefault();
  }

  function drag(event) {
    event.dataTransfer.setData('text', event.target.id);
  }

  function drop(event) {
    event.preventDefault();
    const data = event.dataTransfer.getData('text');
    event.target.appendChild(document.getElementById(data));
  }
</script>
```

## 五、微数据（Microdata）

提供了一种嵌入结构化数据到 HTML 文档中的方式，帮助搜索引擎更好地理解页面内容。
