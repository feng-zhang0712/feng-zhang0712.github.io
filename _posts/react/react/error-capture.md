在 React 中，有多种方式可以捕获异常，以便更好地处理和调试错误。这些方式包括：`try/catch`、`window.onerror`、`error` 事件、`unhandledrejection` 事件、以及 React 提供的 `ErrorBoundary` 组件。

### 1. `try/catch`

#### 原理：
`try/catch` 是 JavaScript 原生的错误捕获机制，可以在同步代码中捕获错误。

#### 使用方式：
```javascript
try {
  // 可能会抛出错误的代码
  someFunction();
} catch (error) {
  console.error("An error occurred:", error);
}
```

#### 捕获范围：
- 只能捕获同步代码中的异常。
- 无法捕获异步代码（如 `setTimeout`、`Promise` 的回调）中的异常。

### 2. `window.onerror`

#### 原理：
`window.onerror` 是一个全局的错误事件处理器，可以捕获未处理的错误。

#### 使用方式：
```javascript
window.onerror = function (message, source, lineno, colno, error) {
  console.error("Global error caught:", message);
  return true; // 阻止错误抛出
};
```

#### 捕获范围：
- 能捕获全局作用域中未处理的同步和异步错误。
- 对于跨域脚本中的错误，需要正确配置 CORS 头，否则错误信息会被屏蔽。

### 3. `error` 事件

#### 原理：
`error` 事件处理器可以捕获各种资源加载错误（如图像、脚本加载失败）。

#### 使用方式：
```javascript
window.addEventListener('error', function (event) {
  console.error("Resource loading error:", event);
}, true);
```

#### 捕获范围：
- 主要用于捕获资源加载错误，如图像、脚本文件加载失败。
- 不能捕获 JavaScript 代码中的运行时错误。

### 4. `unhandledrejection` 事件

#### 原理：
`unhandledrejection` 事件处理器可以捕获未处理的 Promise 拒绝（rejections）。

#### 使用方式：
```javascript
window.addEventListener('unhandledrejection', function (event) {
  console.error("Unhandled promise rejection:", event.reason);
});
```

#### 捕获范围：
- 能捕获未处理的 Promise 拒绝（rejections）。
- 无法捕获同步错误和其他类型的异步错误。

### 5. `ErrorBoundary` 组件

#### 原理：
React 提供了 `ErrorBoundary` 组件用于捕获 React 组件树中的错误。它是通过实现 `componentDidCatch` 生命周期方法来捕获错误。

#### 使用方式：
```jsx
import React, { Component } from 'react';

class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染能够显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 你也可以将错误日志上报给服务器
    console.error("Error captured in ErrorBoundary:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 你可以自定义降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

#### 捕获范围：
- 能捕获 React 组件树中的渲染错误、生命周期方法中的错误，以及子组件中的错误。
- 无法捕获事件处理器中的错误、异步代码中的错误（如 `setTimeout`、`Promise` 回调中的错误）和服务端渲染中的错误。

### 示例：综合使用

下面是一个综合示例，展示了如何在 React 应用中使用这些方式来捕获不同类型的异常：

```jsx
import React, { useState, useEffect } from 'react';
import ErrorBoundary from './ErrorBoundary';

function App() {
  const [state, setState] = useState(null);

  useEffect(() => {
    // 模拟一个未处理的 Promise 拒绝
    Promise.reject(new Error("Unhandled Promise Rejection"));

    // 模拟一个异步错误
    setTimeout(() => {
      throw new Error("Async Error");
    }, 1000);
  }, []);

  const handleClick = () => {
    // 模拟一个同步错误
    try {
      throw new Error("Synchronous Error");
    } catch (error) {
      console.error("Caught by try/catch:", error);
    }
  };

  return (
    <div>
      <button onClick={handleClick}>Trigger Error</button>
      {state}
    </div>
  );
}

function Root() {
  return (
    <ErrorBoundary>
      <App />
    </ErrorBoundary>
  );
}

export default Root;

// 全局错误捕获
window.onerror = function (message, source, lineno, colno, error) {
  console.error("Global error caught:", message);
  return true;
};

window.addEventListener('unhandledrejection', function (event) {
  console.error("Unhandled promise rejection:", event.reason);
});
```

### 总结

在 React 应用中，可以通过多种方式捕获异常：

1. **`try/catch`**：捕获同步代码中的异常。
2. **`window.onerror`**：全局捕获未处理的同步和异步错误。
3. **`error` 事件**：捕获资源加载错误。
4. **`unhandledrejection` 事件**：捕获未处理的 Promise 拒绝。
5. **`ErrorBoundary` 组件**：捕获 React 组件树中的渲染错误和子组件中的错误。

通过结合使用这些方法，你可以全面地捕获和处理不同类型的异常，提高应用的稳定性和用户体验。