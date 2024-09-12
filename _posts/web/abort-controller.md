# AbortController

`AbortController` 是一个用于取消 Web 请求的 API，在现代 Web 开发中非常有用。它允许你取消 Fetch 请求、事件监听器等。

## 一、基本用法

`AbortController` 提供了一种机制，用来在需要时取消异步操作。它的主要方法和属性包括：

- `AbortController` 构造函数：创建一个新的 `AbortController` 对象。
- `signal` 属性：获取 `AbortSignal` 对象，该对象会被传递给需要取消的异步操作。
- `abort` 方法：触发取消操作。

```javascript
const controller = new AbortController();
const signal = controller.signal;

fetch('https://jsonplaceholder.typicode.com/posts', { signal })
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted');
    } else {
      console.error('Fetch error:', error);
    }
  });

// 取消请求
controller.abort();
```

## 二、与 Fetch API 结合使用

### 1. 创建 `AbortController` 和 `AbortSignal`

创建 `AbortController` 实例，并获取其 `signal` 属性。

```javascript
const controller = new AbortController();
const signal = controller.signal;
```

### 2. 将 `signal` 传递给 Fetch 请求

在 Fetch 请求的配置对象中传递 `signal`。

```javascript
fetch('https://jsonplaceholder.typicode.com/posts', { signal })
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted');
    } else {
      console.error('Fetch error:', error);
    }
  });
```

### 3. 取消请求

调用 `AbortController` 的 `abort` 方法来取消请求。

```javascript
controller.abort();
```

## 三、应用场景

### 1. 用户导航离开页面时取消未完成的请求

```javascript
const controller = new AbortController();
const signal = controller.signal;

window.addEventListener('beforeunload', () => {
  controller.abort();
});

fetch('https://jsonplaceholder.typicode.com/posts', { signal })
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted due to page unload');
    } else {
      console.error('Fetch error:', error);
    }
  });
```

### 2. 超时取消请求

在设定时间内未完成的请求将被取消。

```javascript
const controller = new AbortController();
const signal = controller.signal;

const fetchWithTimeout = (url, timeout) => {
  setTimeout(() => controller.abort(), timeout);
  return fetch(url, { signal });
};

fetchWithTimeout('https://jsonplaceholder.typicode.com/posts', 5000)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted due to timeout');
    } else {
      console.error('Fetch error:', error);
    }
  });
```

### 3. 用户界面交互取消请求

用户点击按钮取消正在进行的请求。

```html
<button id="cancelButton">取消请求</button>
<script>
  const controller = new AbortController();
  const signal = controller.signal;

  fetch('https://jsonplaceholder.typicode.com/posts', { signal })
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => {
      if (error.name === 'AbortError') {
        console.log('Fetch aborted by user');
      } else {
        console.error('Fetch error:', error);
      }
    });

  document.getElementById('cancelButton').addEventListener('click', () => {
    controller.abort();
  });
</script>
```

## 四、与其他异步操作结合

### 1. 取消事件监听器

使用 `AbortController` 取消添加的事件监听器。

```javascript
const controller = new AbortController();
const signal = controller.signal;

function handleClick(event) {
  console.log('Button clicked');
}

document.getElementById('myButton').addEventListener('click', handleClick, { signal });

// 取消事件监听器
controller.abort();
```

### 2. 与 `Promise.race` 结合

通过 `AbortController` 和 `Promise.race` 实现超时控制。

```javascript
const controller = new AbortController();
const signal = controller.signal;

const fetchWithTimeout = (url, timeout) => {
  return Promise.race([
    fetch(url, { signal }),
    new Promise((_, reject) =>
      setTimeout(() => {
        controller.abort();
        reject(new Error('Request timed out'));
      }, timeout)
    )
  ]);
};

fetchWithTimeout('https://jsonplaceholder.typicode.com/posts', 5000)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted due to timeout');
    } else {
      console.error('Fetch error:', error);
    }
  });
```

## 五、多次请求合并取消

使用单个 `AbortController` 实例取消多个请求。

```javascript
const controller = new AbortController();
const signal = controller.signal;

Promise.all([
  fetch('https://jsonplaceholder.typicode.com/posts/1', { signal }),
  fetch('https://jsonplaceholder.typicode.com/posts/2', { signal })
])
  .then(responses => Promise.all(responses.map(response => response.json())))
  .then(data => console.log(data))
  .catch(error => {
    if (error.name === 'AbortError') {
      console.log('Fetch aborted');
    } else {
      console.error('Fetch error:', error);
    }
  });

// 取消所有请求
controller.abort();
```
