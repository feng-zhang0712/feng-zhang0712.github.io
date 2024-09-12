# Canvas

## 什么是 Canvas？

`<canvas>` 是 HTML5 引入的一种用于绘制图形的元素。通过 JavaScript API，开发者可以在 `<canvas>` 元素上绘制 2D 和 3D 图形。`canvas` 提供了自由绘制图形、图片、文本和动画的能力，是前端开发中实现动态图形效果的常用工具。

## 基本使用

### 1. 创建一个 Canvas 元素

在 HTML 中创建一个 `<canvas>` 元素，并设置其宽度和高度。

```html
<canvas id="myCanvas" width="500" height="400"></canvas>
```

### 2. 获取绘图上下文

通过 JavaScript 获取 `<canvas>` 元素，并获取其绘图上下文。`canvas` 提供了两种绘图上下文：2D 和 WebGL（3D）。

```javascript
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');
```

## 常用 2D 绘图操作

### 1. 绘制矩形

- `fillRect(x, y, width, height)`：绘制一个填充的矩形。
- `strokeRect(x, y, width, height)`：绘制一个矩形的边框。
- `clearRect(x, y, width, height)`：清除指定矩形区域。

```javascript
ctx.fillStyle = 'red';
ctx.fillRect(10, 10, 100, 50);

ctx.strokeStyle = 'blue';
ctx.strokeRect(130, 10, 100, 50);

ctx.clearRect(250, 10, 100, 50);
```

### 2. 绘制路径

通过创建路径进行复杂图形绘制。

- `beginPath()`：开始一条新路径。
- `moveTo(x, y)`：将画笔移动到指定位置。
- `lineTo(x, y)`：绘制一条从当前位置到指定位置的直线。
- `closePath()`：关闭当前路径。
- `stroke()`：绘制路径。
- `fill()`：填充路径内部。

```javascript
ctx.beginPath();
ctx.moveTo(50, 100);
ctx.lineTo(150, 100);
ctx.lineTo(100, 150);
ctx.closePath();
ctx.stroke();

ctx.beginPath();
ctx.moveTo(200, 100);
ctx.lineTo(300, 100);
ctx.lineTo(250, 150);
ctx.closePath();
ctx.fillStyle = 'green';
ctx.fill();
```

### 3. 绘制圆形和弧线

- `arc(x, y, radius, startAngle, endAngle, anticlockwise)`：绘制一个圆弧。

```javascript
ctx.beginPath();
ctx.arc(100, 200, 50, 0, Math.PI * 2, true); // 绘制一个圆
ctx.stroke();

ctx.beginPath();
ctx.arc(250, 200, 50, 0, Math.PI, false); // 绘制一个半圆
ctx.fillStyle = 'purple';
ctx.fill();
```

### 4. 绘制文本

- `fillText(text, x, y, maxWidth)`：绘制填充文本。
- `strokeText(text, x, y, maxWidth)`：绘制文本轮廓。

```javascript
ctx.font = '30px Arial';
ctx.fillText('Hello Canvas', 50, 300);

ctx.font = '30px Arial';
ctx.strokeText('Hello Canvas', 50, 350);
```

### 5. 绘制图像

- `drawImage(image, dx, dy)`：绘制图像。
- `drawImage(image, dx, dy, dWidth, dHeight)`：绘制缩放的图像。
- `drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight)`：绘制图像的部分区域。

```javascript
const img = new Image();
img.src = 'https://example.com/image.png';
img.onload = function() {
  ctx.drawImage(img, 50, 400, 100, 100); // 绘制图像并缩放
};
```

## 动画和交互

### 1. 动画

通过不断地清除画布并重绘内容，可以创建动画效果。

```javascript
let x = 0;

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  
  ctx.fillStyle = 'blue';
  ctx.fillRect(x, 150, 50, 50);
  
  x += 1;
  if (x > canvas.width) {
    x = 0;
  }
  
  requestAnimationFrame(draw);
}

draw();
```

### 2. 处理用户交互

通过侦听用户事件（如鼠标点击、移动）来创建交互效果。

```javascript
canvas.addEventListener('mousemove', function(event) {
  const rect = canvas.getBoundingClientRect();
  const mouseX = event.clientX - rect.left;
  const mouseY = event.clientY - rect.top;
  
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  ctx.fillStyle = 'orange';
  ctx.fillRect(mouseX - 25, mouseY - 25, 50, 50);
});
```

## 高级使用

### 1. 转换 (Transforms)

通过变换，可以对绘图进行旋转、缩放和平移。

- `translate(x, y)`：平移。
- `rotate(angle)`：旋转。
- `scale(x, y)`：缩放。

```javascript
ctx.save(); // 保存当前状态
ctx.translate(200, 200);
ctx.rotate((Math.PI / 180) * 45);
ctx.fillRect(-25, -25, 50, 50);
ctx.restore(); // 恢复保存的状态
```

### 2. 渐变 (Gradients)

通过创建渐变，可以实现丰富的颜色效果。

- `createLinearGradient(x0, y0, x1, y1)`：线性渐变。
- `createRadialGradient(x0, y0, r0, x1, y1, r1)`：径向渐变。

```javascript
const gradient = ctx.createLinearGradient(0, 0, 200, 0);
gradient.addColorStop(0, 'red');
gradient.addColorStop(1, 'blue');

ctx.fillStyle = gradient;
ctx.fillRect(50, 50, 200, 100);
```

## 优点和缺点

### 优点

- **灵活性强**：可以自由绘制任意图形，适合各种图形和动画需求。
- **与 JavaScript 集成**：方便与其他 JavaScript 库和框架结合使用。
- **无需插件**：直接在浏览器中运行，跨平台兼容。

### 缺点

- **性能问题**：复杂图形和动画可能导致性能下降。
- **学习曲线**：需要掌握大量绘图 API 和图形编程知识。
- **无内置高阶功能**：缺乏高级图形功能，需要开发者自己实现。
