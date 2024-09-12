在前端开发中，为了处理单行或多行文本内容超出容器范围的情况，通常会使用省略号来表示溢出的部分。以下是实现单行和多行文本溢出省略样式的方法。

### 单行文本溢出省略

要实现单行文本溢出省略，可以使用以下 CSS 属性：

- `white-space: nowrap;`：禁止文本换行。
- `overflow: hidden;`：隐藏超出容器范围的文本。
- `text-overflow: ellipsis;`：使用省略号表示溢出的文本。

```css
.single-line-ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  width: 200px; /* 确定的宽度 */
}
```

```html
<div class="single-line-ellipsis">
  这是一个很长的文本示例，用于展示单行文本溢出省略的效果。
</div>
```

### 多行文本溢出省略

要实现多行文本溢出省略，可以使用以下 CSS 属性：

- `display: -webkit-box;`：使用弹性盒模型。
- `-webkit-box-orient: vertical;`：垂直排列子元素。
- `-webkit-line-clamp: 3;`：限制显示的行数。
- `overflow: hidden;`：隐藏超出容器范围的文本。
- `text-overflow: ellipsis;`：使用省略号表示溢出的文本。

```css
.multi-line-ellipsis {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3; /* 显示的行数 */
  overflow: hidden;
  text-overflow: ellipsis;
  width: 200px; /* 确定的宽度 */
  height: 60px; /* 确定的高度（可选，根据显示行数计算） */
  line-height: 20px; /* 行高 */
}
```

```html
<div class="multi-line-ellipsis">
  这是一个很长的文本示例，用于展示多行文本溢出省略的效果。我们希望这个文本在第三行之后使用省略号来表示内容的溢出部分。
</div>
```

### 使用纯 CSS 实现多行文本溢出省略（兼容性较好）

虽然 `-webkit-line-clamp` 是一种方便的方法，但它目前仅在 WebKit 内核（如 Chrome 和 Safari）上支持。对于更好的浏览器兼容性，可以使用其他 CSS 技巧来实现多行文本溢出省略。

**方法一：使用 `::after` 伪元素**

```css
.multi-line-ellipsis-alt {
  position: relative;
  max-height: 60px; /* 确定的高度（根据显示行数计算） */
  line-height: 20px; /* 行高 */
  overflow: hidden;
}

.multi-line-ellipsis-alt::after {
  content: '...';
  position: absolute;
  bottom: 0;
  right: 0;
  background: white;
  padding: 0 5px;
}
```

```html
<div class="multi-line-ellipsis-alt">
  这是一个很长的文本示例，用于展示多行文本溢出省略的效果。我们希望这个文本在第三行之后使用省略号来表示内容的溢出部分。
</div>
```

### 总结

- **单行文本溢出省略**：使用 `white-space: nowrap;`、`overflow: hidden;` 和 `text-overflow: ellipsis;`。
- **多行文本溢出省略（WebKit）**：使用 `display: -webkit-box;`、`-webkit-box-orient: vertical;`、`-webkit-line-clamp` 和 `overflow: hidden;`。
- **多行文本溢出省略（兼容性较好）**：使用 `max-height`、`line-height` 和伪元素 `::after`。

通过这些方法，你可以有效地处理单行和多行文本内容超出容器范围的情况，让页面显示更加美观和整洁。