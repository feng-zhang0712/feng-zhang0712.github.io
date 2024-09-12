`<iframe>`（内联框架）是 HTML 中的一个元素，用于在网页中嵌入另一个 HTML 页面。它可以用于多种用途，例如嵌入外部内容、广告、视频、地图等。以下是对 `<iframe>` 的详细介绍：

### 基本用法

**语法：**

```html
<iframe src="URL"></iframe>
```

- **`src`**：要嵌入的页面的 URL。

**示例：**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Iframe 示例</title>
</head>
<body>
  <h1>这是一个包含 iframe 的页面</h1>
  <iframe src="https://www.example.com" width="600" height="400" title="Example"></iframe>
</body>
</html>
```

### 常用属性

1. **`src`**：
   - 指定要嵌入的页面的 URL。
   - 示例：`<iframe src="https://www.example.com"></iframe>`

2. **`width` 和 `height`**：
   - 指定 iframe 的宽度和高度。
   - 示例：`<iframe src="https://www.example.com" width="600" height="400"></iframe>`

3. **`title`**：
   - 提供 iframe 的描述，改善可访问性。
   - 示例：`<iframe src="https://www.example.com" title="Example"></iframe>`

4. **`name`**：
   - 为 iframe 指定一个名称，以便在链接或脚本中引用。
   - 示例：`<iframe src="https://www.example.com" name="iframeName"></iframe>`

5. **`sandbox`**：
   - 应用额外的安全限制，如禁止脚本执行、表单提交等。可以用多个值组合使用。
   - 示例：`<iframe src="https://www.example.com" sandbox="allow-same-origin allow-scripts"></iframe>`

6. **`allow`**：
   - 指定 iframe 内允许的特性，如地理位置访问、相机、麦克风等。
   - 示例：`<iframe src="https://www.example.com" allow="geolocation; microphone; camera"></iframe>`

7. **`loading`**：
   - 控制 iframe 的加载行为。可能的值有 `lazy`（延迟加载，默认值）、`eager`（立即加载）。
   - 示例：`<iframe src="https://www.example.com" loading="lazy"></iframe>`

### 安全和限制

使用 `<iframe>` 时需要注意一些安全问题，特别是在嵌入外部内容时:

1. **跨站脚本（XSS）**：
   - 嵌入的内容可能包含恶意脚本，威胁主页面的安全。使用 `sandbox` 属性可以减轻这种风险。

2. **同源策略**：
   - 由于浏览器的同源策略，嵌入的页面无法访问主页面的 DOM 和 JavaScript，反之亦然。这可以防止跨站攻击。

3. **Iframe 嵌套**：
   - 嵌套太多层的 iframe 会影响页面性能和用户体验。通常建议限制嵌套层数。

### 示例：嵌入 YouTube 视频

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>嵌入 YouTube 视频</title>
</head>
<body>
  <h1>这是一个包含 YouTube 视频的页面</h1>
  <iframe width="560" height="315" src="https://www.youtube.com/embed/dQw4w9WgXcQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</body>
</html>
```

### 使用 `sandbox` 属性

`sandbox` 属性提供了更细粒度的控制，可以用于增强嵌入内容的安全性：

```html
<iframe src="https://www.example.com" sandbox="allow-same-origin allow-scripts"></iframe>
```

- **`allow-same-origin`**：允许 iframe 内容访问相同来源的页面。
- **`allow-scripts`**：允许在 iframe 中运行脚本。
- **其他值**包括 `allow-forms`（允许表单提交）、`allow-popups`（允许弹出窗口）、`allow-modals`（允许模式对话框）以及 `allow-top-navigation`（允许导航到顶级浏览上下文）。

### 与 JavaScript 的交互

通过 JavaScript 可以与 iframe 进行交互，以下是一些常见的操作方法：

1. **访问 iframe 中的内容**：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>访问 iframe 内容</title>
</head>
<body>
  <iframe id="myIframe" src="https://www.example.com" width="600" height="400" title="Example"></iframe>
  <script>
    const iframe = document.getElementById('myIframe');
    iframe.onload = function() {
      const iframeDocument = iframe.contentDocument || iframe.contentWindow.document;
      console.log(iframeDocument.body.innerHTML);
    };
  </script>
</body>
</html>
```

2. **改变 iframe 的 src 属性**：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>改变 iframe 源</title>
</head>
<body>
  <iframe id="myIframe" src="https://www.example.com" width="600" height="400" title="Example"></iframe>
  <button onclick="changeSrc()">改变 iframe 源</button>
  <script>
    function changeSrc() {
      document.getElementById('myIframe').src = "https://www.another-example.com";
    }
  </script>
</body>
</html>
```

### 总结

`<iframe>` 是一个强大的工具，用于在网页中嵌入外部内容。通过了解和正确使用其属性和安全措施，可以有效地利用 iframe 来实现各类功能，同时保护页面的安全性和性能。在实际应用中，选择合适的嵌入方式和安全策略至关重要。