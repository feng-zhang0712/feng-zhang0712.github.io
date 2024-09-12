# document.write 和 innerHTML

在 HTML 文档中，JavaScript 提供了多种方法来动态更新和操作文档内容。了解这些方法的区别和用法对于前端开发者来说非常重要。

## 1. `document.write`

`document.write` 方法用于向文档流中写入内容。它是在文档加载时执行的，如果在文档完全加载后调用该方法，它会覆盖整个文档内容。

```javascript
document.write('<p>This is written by document.write</p>');
```

**注意**：在文档加载完成后（例如在 `window.onload` 事件后）调用 `document.write` 会导致页面被完全覆盖，通常不建议这样做。

- `document.write` 会当作 HTML 代码解析，不会转义。
- 如果页面已经解析完成（ `DOMContentLoaded` 事件发生之后），再调用 `write` 方法，它会先调用 `open` 方法，擦除当前文档所有内容，然后再写入。
- 应该尽量避免使用 `document.write` 方法。

## 2. `document.writeln`

`document.writeln` 与 `document.write` 类似，但它会在写入的内容末尾添加一个换行符（`\n`），在HTML中表现为一个换行。

## 3. `innerHTML`

`innerHTML` 属性允许你获取或设置元素的 HTML 内容。它可以用于插入 HTML 片段，动态更新 DOM 内容。

```javascript
document.getElementById('example').innerHTML = '<p>This is set by innerHTML</p>';
```

- 如果将 `innerHTML` 属性设为空，等于删除所有它包含的所有节点。
- 读取属性值的时候，如果文本节点包含&、小于号（<）和大于号（>），`innerHTML` 属性会将它们转为实体形式 `&amp;`、`&lt;`、`&gt;`。如果想得到原文，建议使用 `element.textContent` 属性。
- 写入的时候，如果插入的文本包含 HTML 标签，会被解析成为节点对象插入 DOM，如果文本之中含有 `<script>` 标签，虽然可以生成script节点，但是插入的代码不会执行。

## 4. `innerText`

获取文本内容时：

- 会将元素的文本内容，包括所有子节点的文本内容，以可见形式呈现出来，即会忽略掉 HTML 标签。
- 还会处理一些样式，例如 `<br>` 标签会被换行显示，`<script>` 标签中的内容会被忽略。
- 它会解析 HTML 标签，并呈现最终的文本内容。

设置文本内容时：

- 会将文本内容直接插入到元素中，不会解析 HTML 标签。
- 设置的内容会被视为纯文本，例如 `<br>` 标签会被显示为 `<br>` 字符串。
- 它会将文本内容作为纯文本插入，不会进行 HTML 解析。

如果需要获取元素的可见文本内容，并且需要处理一些样式，则可以使用 `innerText`。

## 5. `textContent`

`textContent` 属性允许你获取或设置元素的文本内容。与 `innerHTML` 不同，`textContent` 会将所有HTML标签视为纯文本。

**用法**：

```javascript
document.getElementById('example').textContent = 'This is set by textContent';
```

- `textContent` 不会解析 HTML 标签，因此它更安全，不会引入 XSS 攻击。

如果需要获取元素的原始文本内容，包括所有 HTML 标签，则可以使用 `textContent`。
