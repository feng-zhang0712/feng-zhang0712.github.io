---
layout: post
title: 防抖和节流
categories: blog
tags: React
---

防抖（Debouncing）和节流（Throttling）是前端性能优化中常用的技术，特别是在处理高频率触发的事件时。它们可以有效地减少函数调用次数，提升应用的性能和响应速度。

## 一、防抖（Debouncing）

### 1.1 概念

防抖是一种确保在一段时间内某个操作不再重复触发后才执行该操作的技术。它通常用于处理用户输入等频繁触发的事件。防抖可以有效减少不必要的函数调用，提升性能。

### 1.2 实现原理

防抖的实现原理是设置一个定时器（timeout），每次事件触发时重置定时器，只有在定时器到期时才执行函数。如果在定时器到期之前事件再次触发，则重新开始计时。

以下是一个简单的防抖函数实现。

```javascript
function debounce(func, wait) {
  let timeout;
  return function(...args) {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      func.apply(this, args);
    }, wait);
  };
}
```

### 1.3 应用场景

- **搜索框输入**：在用户停止输入一定时间后再发起搜索请求，以减少不必要的请求数量。
- **窗口大小调整**：在用户停止调整窗口大小后再执行相关操作，以避免频繁触发回调函数。

以下是一个在 React 中使用防抖的示例，处理输入框的变化事件：

```jsx
import React, { useState } from 'react';
import debounce from 'lodash.debounce';

function SearchInput() {
  const [query, setQuery] = useState('');

  const handleChange = debounce((event) => {
    setQuery(event.target.value);
    // 发起 API 请求或其他操作
    console.log('Fetching data for:', event.target.value);
  }, 300);

  return (
    <input
      type="text"
      onChange={handleChange}
      placeholder="Search..."
    />
  );
}

export default SearchInput;
```

在这个示例中，`handleChange` 函数在用户停止输入 300 毫秒后才执行，从而减少了不必要的 API 请求。

### 二、节流（Throttling）

### 2.1 概念

节流是一种确保在一定时间间隔内函数只执行一次的技术。它通常用于处理滚动、窗口调整大小等高频触发的事件。节流可以限制函数调用的频率，防止性能问题。

### 2.2 实现原理

节流的实现原理是限制函数在指定时间间隔内只能执行一次，通过设置一个标志（flag）或记录上次执行的时间来控制函数调用。

以下是一个简单的节流函数实现：

```javascript
function throttle(func, limit) {
  let lastFunc;
  let lastRan;
  return function(...args) {
    if (!lastRan) {
      func.apply(this, args);
      lastRan = Date.now();
    } else {
      clearTimeout(lastFunc);
      lastFunc = setTimeout(() => {
        if (Date.now() - lastRan >= limit) {
          func.apply(this, args);
          lastRan = Date.now();
        }
      }, limit - (Date.now() - lastRan));
    }
  };
}
```

### 2.4 应用场景

- **滚动事件**：在用户滚动页面时限制回调函数的执行频率，以提高页面性能。
- **窗口大小调整**：在用户调整窗口大小时限制回调函数的执行频率，以减少不必要的计算。

以下是一个在 React 中使用节流的示例，处理窗口大小调整事件：

```jsx
import React, { useState, useEffect } from 'react';
import throttle from 'lodash.throttle';

function WindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  const handleResize = throttle(() => {
    setSize({
      width: window.innerWidth,
      height: window.innerHeight,
    });
  }, 1000);

  useEffect(() => {
    window.addEventListener('resize', handleResize);
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return (
    <div>
      <h1>Window Size</h1>
      <p>Width: {size.width}px</p>
      <p>Height: {size.height}px</p>
    </div>
  );
}

export default WindowSize;
```

在这个示例中，`handleResize` 函数在窗口大小变化时每秒最多执行一次，从而减少了不必要的状态更新。
