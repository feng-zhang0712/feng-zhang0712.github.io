# 方式按钮多次点击

## 一、使用 CSS 方式及按钮的 `disabled` 属性

### 1. 禁用按钮

在提交数据后立即禁用按钮，直到请求完成或一定时间后再启用按钮。

```html
<button id="submitButton" onclick="handleSubmit()">提交</button>

<script>
  function handleSubmit() {
    const button = document.getElementById('submitButton');
    // 禁用按钮
    button.disabled = true;

    // 模拟数据提交
    setTimeout(() => {
      console.log('数据已提交');
      // 启用按钮
      button.disabled = false;
    }, 2000); // 假设请求需要2秒钟完成
  }
</script>
```

### 2. 使用 CSS 样式

通过 设置样式属性 `pointer-events:none`，可以指定元素永远不会成为鼠标事件的 target。

```html
<style>
  .disabled-button {
    pointer-events: none;
  }
</style>

<button id="submitButton" onclick="handleSubmit()">提交</button>

<script>
  function handleSubmit() {
    const button = document.getElementById('submitButton');
    // 禁用按钮并添加禁用样式
    button.classList.add('disabled-button');

    // 模拟数据提交
    setTimeout(() => {
      console.log('数据已提交');
      // 启用按钮并移除禁用样式
      button.classList.remove('disabled-button');
    }, 2000); // 假设请求需要2秒钟完成
  }
</script>
```

注意：使用pointer-events来阻止元素成为鼠标事件目标不一定意味着元素上的事件侦听器永远不会触发。如果元素后代明确指定了pointer-events属性并允许其成为鼠标事件的目标，那么指向该元素的任何事件在事件传播过程中都将通过父元素，并以适当的方式触发其上的事件侦听器。这也就意味着，在这种情况下，鼠标事件将在捕获或冒泡阶段触发父元素的事件侦听器。

## 二、使用遮罩

在请求进行时在页面上添加一个遮罩，防止用户进行任何操作。

```html
<style>
  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: none;
    z-index: 1000;
  }
</style>

<div id="overlay" class="overlay"></div>
<button id="submitButton" onclick="handleSubmit()">提交</button>

<script>
  function handleSubmit() {
    const overlay = document.getElementById('overlay');
    // 显示遮罩
    overlay.style.display = 'block';

    // 模拟数据提交
    setTimeout(() => {
      console.log('数据已提交');
      // 隐藏遮罩
      overlay.style.display = 'none';
    }, 2000); // 假设请求需要2秒钟完成
  }
</script>
```

## 三、防抖（Debounce）

防抖函数在用户停止触发事件后一段时间才会执行回调函数。如果在这段时间内再次触发事件，则重新计时。

```html
<button id="submitButton">提交</button>

<script>
  function debounce(func, delay) {
    let timeoutID;
    return function(...args) {
      clearTimeout(timeoutID);
      timeoutID = setTimeout(() => {
        func.apply(this, args);
      }, delay);
    };
  }

  function handleSubmit() {
    console.log('数据已提交');
  }

  const debouncedSubmit = debounce(handleSubmit, 2000); // 停止点击2秒钟后触发
  document.getElementById('submitButton').addEventListener('click', debouncedSubmit);
</script>
```

## 四、节流（Throttle）

节流函数确保在一定时间间隔内只调用一次回调函数。

```html
<button id="submitButton">提交</button>

<script>
  function throttle(func, delay) {
    let lastTime = 0;
    return function(...args) {
      const now = Date.now();
      if (now - lastTime >= delay) {
        lastTime = now;
        func.apply(this, args);
      }
    };
  }

  function handleSubmit() {
    console.log('数据已提交');
  }

  const throttledSubmit = throttle(handleSubmit, 2000); // 每2秒钟最多触发一次
  document.getElementById('submitButton').addEventListener('click', throttledSubmit);
</script>
```

## 五、使用标志位

通过设置标志位来跟踪请求状态，防止在请求完成前再次提交。

```html
<button id="submitButton" onclick="handleSubmit()">提交</button>

<script>
  let isSubmitting = false;

  function handleSubmit() {
    if (isSubmitting) return;

    isSubmitting = true;
    console.log('数据提交中...');

    // 模拟数据提交
    setTimeout(() => {
      console.log('数据已提交');
      isSubmitting = false; // 请求完成后重置标志位
    }, 2000); // 假设请求需要2秒钟完成
  }
</script>
```

## 六、后端防重处理

后端可以通过以下方法进行防重处理，确保同一个用户在短时间内不能重复提交相同的数据。

### 1. Token机制

每次提交数据时生成唯一的Token，后端验证Token的唯一性，确保每个Token只能使用一次。

```javascript
// 伪代码示例
let tokens = new Set();

function handleRequest(req, res) {
  const token = req.body.token;
  
  if (tokens.has(token)) {
    return res.status(400).send('重复提交');
  }
  
  tokens.add(token);
  
  // 模拟处理请求
  setTimeout(() => {
    tokens.delete(token);
    res.send('请求成功');
  }, 2000);
}
```

### 2. 时间戳

记录每次请求的时间戳，限制同一用户在一定时间内只能提交一次请求。

```javascript
// 伪代码示例
let lastSubmitTime = {};

function handleRequest(req, res) {
  const userId = req.user.id;
  const currentTime = Date.now();

  if (lastSubmitTime[userId] && (currentTime - lastSubmitTime[userId] < 2000)) {
    return res.status(429).send('请求过于频繁，请稍后再试');
  }

  lastSubmitTime[userId] = currentTime;
  
  // 处理请求
  // ...
  res.send('请求成功');
}
```
