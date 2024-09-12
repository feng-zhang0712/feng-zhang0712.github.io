# requestAnimationFrame

## 一、概念

### 1.1 帧生命周期

在浏览器中，一帧（Frame）是渲染引擎进行一次完整的屏幕刷新操作，通常每秒渲染60帧（即60 FPS）。为了达到流畅的用户体验，浏览器需要在每秒内完成大量的操作。

![Life of a frame](../../../assets/life-of-a-frame.webp)
*图片来源：[@paul_irish](https://medium.com/@paul_irish/requestanimationframe-scheduling-for-nerds-9c57f7438ef4)*

通过上图可以看出，浏览器一帧内要执行的任务有：

1. **处理输入事件（Input events）**：处理用户的输入事件，如触摸事件、鼠标点击、移动，键盘输入等。
2. **脚本执行（JS）**：例如，执行由`setTimeout`、`setInterval`设置的回调函数。
3. **帧开始（Begin frame）**：每一帧事件（Per frame events），例如 window resize、scroll、动画相关的事件等。
4. **rAF（requestAnimationFrame）**
5. **布局（Layout）**：计算样式（Recalculate style）和更新布局（Update Layout），如果某个元素的尺寸或位置发生变化，浏览器需要重新计算受影响部分的布局（称为“回流”或“重排”）。
6. **渲染（Paint）**：合成更新（Compositing update）、重绘部分节点（Paint invalidation）等。

### 1.2 `requestAnimationFrame`

`requestAnimationFrame` 是一个 Web API，用于在浏览器的下一次重绘之前执行一次回调函数。这使得开发者可以在动画帧之间进行高效的动画更新。通过利用浏览器的重绘时机，`requestAnimationFrame` 可以帮助创建流畅的动画，同时避免不必要的性能开销。

## 二、基本用法

`requestAnimationFrame` 的基本使用方式非常简单：

```javascript
function animate() {
  // 更新动画的逻辑
  console.log('Animating...');
  
  // 请求下一帧动画
  requestAnimationFrame(animate);
}

// 开始动画
requestAnimationFrame(animate);
```

该函数接受一个回调函数作为参数，该回调函数会在下一次重绘之前被调用。

## 三、优点和特性

### 3.1 高效的动画更新

`requestAnimationFrame` 会在浏览器准备好重绘时调用回调函数，这通常是每秒 60 次（即 60fps），但具体频率可能根据硬件和浏览器的性能而变化。通过这种方式，`requestAnimationFrame` 能够提供流畅的动画，而不会导致浏览器过度渲染。

### 3.2 节能模式

当页面不可见或隐藏时，如切换到另一个 Tab，`requestAnimationFrame` 会暂停调用回调函数。这与传统的 `setTimeout` 或 `setInterval` 不同，有助于节省资源和电池寿命。

### 3.3 帧同步

`requestAnimationFrame` 与浏览器的刷新率同步，确保动画在最佳的时机执行，从而提供更流畅的视觉体验。

## 四、帧率控制

虽然 `requestAnimationFrame` 默认提供每秒 60 帧的动画更新，但有时可能需要控制帧率。例如，可以通过计时器实现自定义帧率：

```javascript
let lastTimestamp = 0;
const fps = 30; // 自定义帧率

function animate(timestamp) {
  const progress = timestamp - lastTimestamp;
  if (progress >= 1000 / fps) {
    lastTimestamp = timestamp;
    // 更新动画的逻辑
    console.log('Animating at 30fps...');
  }

  // 请求下一帧动画
  requestAnimationFrame(animate);
}

// 开始动画
requestAnimationFrame(animate);
```

在这个示例中，`animate` 函数使用时间戳来计算帧之间的间隔，从而实现自定义的帧率控制。

## 五、结合 `cancelAnimationFrame`

有时需要停止动画，可以使用 `cancelAnimationFrame` 来取消先前的动画请求：

```javascript
let animationId;

function startAnimation() {
  function animate() {
    // 更新动画的逻辑
    console.log('Animating...');
    // 请求下一帧动画
    animationId = requestAnimationFrame(animate);
  }

  // 开始动画
  animationId = requestAnimationFrame(animate);
}

function stopAnimation() {
  // 取消动画
  cancelAnimationFrame(animationId);
}

// 开始动画
startAnimation();

// 停止动画
setTimeout(stopAnimation, 5000); // 5秒后停止动画
```

在这个示例中，`startAnimation` 函数启动动画，并保存 `requestAnimationFrame` 返回的动画 ID。`stopAnimation` 函数使用 `cancelAnimationFrame` 取消动画。

## 六、使用 `requestAnimationFrame` 优化 DOM 操作

`requestAnimationFrame` 还可以用于优化 DOM 操作，通过将多个 DOM 操作合并到一个重绘周期中。例如，在处理滚动事件时，可以使用 `requestAnimationFrame` 来提高性能。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Scroll Optimization</title>
  <style>
    body {
      height: 2000px;
    }
    .box {
      width: 100px;
      height: 100px;
      background-color: red;
      position: fixed;
      top: 0;
      left: 0;
    }
  </style>
</head>
<body>
  <div class="box"></div>
  <script>
    const box = document.querySelector('.box');
    let lastKnownScrollPosition = 0;
    let ticking = false;

    function updateBox(scrollPos) {
      box.style.transform = `translateY(${scrollPos}px)`;
    }

    document.addEventListener('scroll', () => {
      lastKnownScrollPosition = window.scrollY;

      if (!ticking) {
        requestAnimationFrame(() => {
          updateBox(lastKnownScrollPosition);
          ticking = false;
        });

        ticking = true;
      }
    });
  </script>
</body>
</html>
```

在这个示例中，使用 `requestAnimationFrame` 将滚动事件的处理与浏览器的重绘同步，从而提高滚动性能。
