# 重流和重绘

## 一、重流（Reflow）

重流（Reflow），也称为布局（Layout），是指浏览器重新计算页面元素的位置和几何属性的过程。任何影响元素尺寸、位置、或者某些属性的改变都会触发重流，例如：

- 添加或删除DOM元素
- 改变元素的大小（如宽度、高度、边距、填充等）
- 改变元素的字体大小
- 改变元素的内容（如文本）
- 通过CSS或JavaScript改变元素的显示状态（如display属性）

重流是一种开销较大的操作，因为它可能会影响整个页面的布局，尤其在复杂的页面中，重流的代价会更高。

## 二、重绘（Repaint）

重绘（Repaint）是指浏览器重新绘制页面元素的外观，但不涉及其布局的改变。任何导致元素外观改变但不影响其几何属性的操作都会触发重绘，例如：

- 改变元素的颜色（如背景色、边框色等）
- 改变元素的透明度
- 改变元素的可见性（如visibility属性）

重绘的开销相对较小，因为它只涉及元素的外观更新，但仍然会占用浏览器的渲染资源。

## 三、减少重流和重绘

为了优化页面性能，减少重流和重绘的频率和范围是非常重要的。以下是一些减少重流和重绘的方法：

### 3.1 合并DOM操作

每次对DOM进行操作（如添加、删除或修改元素）都会触发重流或重绘。合并DOM操作可以减少这些操作的频率：

- **批量操作DOM**：将多次的DOM操作合并为一次，例如使用`documentFragment`批量添加元素。
- **离线DOM操作**：在操作DOM之前，先将元素从文档流中移除，操作完成后再添加回去。例如使用`display: none`将元素隐藏，操作完成后再显示。

```javascript
// 批量操作DOM
let fragment = document.createDocumentFragment();
for (let i = 0; i < 1000; i++) {
  let newDiv = document.createElement('div');
  newDiv.textContent = `Item ${i}`;
  fragment.appendChild(newDiv);
}
document.body.appendChild(fragment);
```

### 3.2 减少样式计算

每次修改样式都会导致浏览器重新计算样式，从而可能触发重流或重绘。减少样式计算的频率和复杂度，可以减少重流和重绘：

- **减少样式的逐个修改**：通过修改class或使用`cssText`一次性改变多个样式属性。
- **避免触发同步布局**：避免在循环中频繁读取和写入DOM属性，例如读取`offsetWidth`、`offsetHeight`等属性会导致强制重流。

```javascript
// 避免逐个修改样式
element.style.cssText = 'width: 100px; height: 100px; background-color: red;';

// 避免在循环中频繁读取和写入DOM
let totalHeight = 0;
for (let i = 0; i < elements.length; i++) {
  totalHeight += elements[i].offsetHeight;
}
```

### 3.3 使用CSS3硬件加速

某些CSS3属性，如`transform`、`opacity`和`translate3d`，可以利用GPU加速渲染，减少重流和重绘的开销：

- **使用transform替代top/left**：使用`transform: translate()`替代`top`和`left`，可以避免触发重流。
- **使用opacity替代visibility**：使用`opacity`替代`visibility`，不会影响布局，只会触发重绘。

```css
/* 使用transform替代top/left */
.element {
  transform: translate(100px, 50px);
}

/* 使用opacity替代visibility */
.element {
  opacity: 0.5;
}
```

### 3.4 适当的CSS选择器

复杂的CSS选择器会增加样式计算的开销，选择器越具体，计算效率越高：

- **避免使用通用选择器**：如`*`选择器，会导致浏览器遍历所有元素。
- **避免过于深层的选择器**：尽量避免使用深层次的嵌套选择器，如`.parent .child .grandchild`。

```css
/* 避免使用通用选择器 */
body * {
  color: red;
}

/* 使用具体选择器 */
.container > .item {
  color: blue;
}
```

### 3.5 优化动画和过渡

动画和过渡是页面中常见的重绘和重流触发因素，优化动画可以减少开销：

- **使用CSS动画和过渡**：尽量使用CSS3动画和过渡代替JavaScript动画，因为CSS动画可以利用GPU加速。
- **减少动画区域**：尽量减少动画和过渡的区域，减少重绘范围。

```css
/* 使用CSS3动画和过渡 */
.element {
  transition: transform 0.5s ease;
}

.element:hover {
  transform: translate3d(100px, 0, 0);
}
```

参考：

- [重流和重绘](https://wangdoc.com/javascript/bom/engine#%E9%87%8D%E6%B5%81%E5%92%8C%E9%87%8D%E7%BB%98)