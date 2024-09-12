自闭合标签（Self-Closing Tags）在 HTML 中用于表示没有内容的元素。这些标签在 HTML4 中称为“空元素（Empty Elements）”，而在 HTML5 中，自闭合标签的概念有所扩展。自闭合标签不需要关闭标签（即不需要成对出现），它们在打开标签内使用一个斜杠（`/`）来表示结束。

以下是 HTML 中常见的自闭合标签：

### 1. `<img>`

用于嵌入图像。

```html
<img src="image.jpg" alt="Description" />
```

### 2. `<br>`

用于插入换行符。

```html
<p>This is a paragraph.<br />This is a new line.</p>
```

### 3. `<hr>`

用于插入水平线（水平分隔符）。

```html
<p>This is some text.</p>
<hr />
<p>This is text after the horizontal line.</p>
```

### 4. `<input>`

用于输入控件，如文本框、复选框、单选按钮等。

```html
<input type="text" name="username" />
<input type="checkbox" name="subscribe" />
```

### 5. `<link>`

用于定义与文档的关系；通常用于链接外部样式表。

```html
<link rel="stylesheet" href="styles.css" />
```

### 6. `<meta>`

用于定义文档的元数据；通常用于指定文档的字符集、描述、关键字、作者等。

```html
<meta charset="UTF-8" />
<meta name="description" content="A brief description of the page" />
```

### 7. `<area>`

定义图像地图中的区域。

```html
<img src="image-map.jpg" usemap="#map" />
<map name="map">
  <area shape="rect" coords="34,44,270,350" alt="Description" href="example.html" />
</map>
```

### 8. `<base>`

用于指定页面中所有相对 URL 的基准 URL。

```html
<base href="https://www.example.com/" />
```

### 9. `<col>`

用于定义表格中的列属性，其父元素是 `<colgroup>`。

```html
<table>
  <colgroup>
    <col span="2" style="background-color:yellow" />
    <col style="background-color:lightgrey" />
  </colgroup>
  <tr>
    <td>Data 1</td>
    <td>Data 2</td>
    <td>Data 3</td>
  </tr>
</table>
```

### 10. `<source>`

用于指定多媒体元素（如 `<video>` 和 `<audio>`）的多个资源。

```html
<video controls>
  <source src="movie.mp4" type="video/mp4" />
  <source src="movie.ogg" type="video/ogg" />
  Your browser does not support the video tag.
</video>
```

### 11. `<wbr>`

表示可能的换行点，浏览器可以选择在此位置换行。

```html
<p>Thisisaverylongword<wbr>thatmightneedbreaking.</p>
```

### 注意事项

- 在 HTML5 中，自闭合标签可以省略斜杠（`/`），例如 `<img>` 和 `<img />` 都是有效的。
- 自闭合标签不包含任何内容，因此无法在它们之间插入文本或其他元素。

### 示例

以下是一个包含多个自闭合标签的 HTML 示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <meta name="description" content="A page with self-closing tags example" />
  <title>Self-Closing Tags Example</title>
  <link rel="stylesheet" href="styles.css" />
</head>
<body>
  <h1>Self-Closing Tags Example</h1>
  <img src="image.jpg" alt="An example image" />
  <p>This is some text.<br />This text is on a new line.</p>
  <hr />
  <input type="text" name="username" placeholder="Enter your username" />
  <input type="checkbox" name="subscribe" /> Subscribe to newsletter
</body>
</html>
```

通过合理使用自闭合标签，可以简化 HTML 代码，并确保页面结构更清晰。