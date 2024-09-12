在 HTML 中，元素可以分为行内元素、块级元素和空元素三种类型。

### 行内元素（Inline Elements）

行内元素不会在其前后创建新的行，它们通常用于包含文本或其他行内元素。行内元素通常不会破坏文档的流布局，因此它们可以与其他行内元素和文本内容并排显示。

#### 常见的行内元素

- `<a>`：锚链接
- `<span>`：通用行内容器
- `<img>`：图像
- `<strong>`：加粗文本
- `<em>`：斜体文本
- `<input>`：输入字段
- `<label>`：标签
- `<abbr>`：缩写
- `<cite>`：引用

#### 特点

- **不生成换行**：行内元素不会在其前后生成换行，多个行内元素会在一行内排列。
- **宽度和高度**：行内元素的宽度和高度通常由其内容决定，无法手动设置宽度和高度（部分例外，如 `<img>`）。
- **布局特性**：行内元素的 `padding` 和 `margin` 只会影响水平方向，垂直方向的 `padding` 和 `margin` 无法改变行高。

#### 示例

```html
<p>This is an <a href="#">inline link</a>.</p>
<p><span>Span</span> elements are also inline.</p>
```

### 块级元素（Block Elements）

块级元素会在其前后创建新的行，通常用于创建布局结构。块级元素可以包含其他块级元素和行内元素。

#### 常见的块级元素

- `<div>`：通用块级容器
- `<p>`：段落
- `<h1>` - `<h6>`：标题
- `<ul>`：无序列表
- `<ol>`：有序列表
- `<li>`：列表项
- `<section>`：章节
- `<article>`：文章
- `<header>`：页眉
- `<footer>`：页脚
- `<main>`：主内容
- `<nav>`：导航
- `<aside>`：侧边栏

#### 特点

- **生成换行**：块级元素会在其前后生成换行，占满其父元素的整个宽度。
- **宽度和高度**：可以手动设置宽度和高度，默认宽度为其父元素的 100%。
- **布局特性**：`padding` 和 `margin` 可以在所有方向上生效。

#### 示例

```html
<div>
  <p>This is a block-level paragraph.</p>
  <div>This is a block-level div.</div>
</div>
```

### 空元素（Void Elements）

空元素是指那些没有内容、没有闭合标签的元素。这些元素一般用于自闭合的标记，如元数据标签、换行符、图像等。

#### 常见的空元素

- `<br>`：换行符
- `<hr>`：水平线
- `<img>`：图像
- `<input>`：输入字段
- `<link>`：链接到外部资源
- `<meta>`：元数据
- `<source>`：媒体资源

#### 特点

- **没有内容**：空元素没有内容，不需要闭合标签。
- **自闭合**：可以直接在开始标签中结束，常用于需要单独插入的标记。

#### 示例

```html
<p>This is a line break<br>and this is after the break.</p>
<img src="image.jpg" alt="An image">
<hr>
```

### 行内元素与块级元素的转换

在 CSS 中，可以通过设置 `display` 属性来改变元素的显示方式。例如，可以将一个行内元素转换为块级元素，或者将块级元素转换为行内元素。

#### 示例

```html
<style>
  .inline-to-block {
    display: block;
  }
  .block-to-inline {
    display: inline;
  }
</style>

<p class="inline-to-block">This inline element is now a block element.</p>
<div class="block-to-inline">This block element is now an inline element.</div>
```
