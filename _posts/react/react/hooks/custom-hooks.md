在 React 中，自定义 Hook 是一种重用状态逻辑的强大工具。它允许你将组件中重复的逻辑抽取出来，并在多个组件中共享。以下是一些常用的自定义 Hook 及其实现示例。

### 1. `useFetch` - 数据获取

`useFetch` 是一个用于从 API 获取数据的自定义 Hook。它处理数据请求、加载状态和错误状态。

```javascript
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (error) {
        setError(error);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

export default useFetch;
```

### 2. `useLocalStorage` - 本地存储

`useLocalStorage` 是一个用于在本地存储中保存和读取数据的自定义 Hook。

```javascript
import { useState } from 'react';

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = value => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

export default useLocalStorage;
```

### 3. `useDebounce` - 防抖处理

`useDebounce` 是一个用于防抖处理的自定义 Hook，常用于处理用户输入等频繁触发的事件。

```javascript
import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

export default useDebounce;
```

### 4. `usePrevious` - 获取前一个状态

`usePrevious` 是一个用于获取前一个状态值的自定义 Hook。

```javascript
import { useRef, useEffect } from 'react';

function usePrevious(value) {
  const ref = useRef();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}

export default usePrevious;
```

### 5. `useToggle` - 切换布尔值

`useToggle` 是一个用于切换布尔值状态的自定义 Hook。

```javascript
import { useState, useCallback } from 'react';

function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => {
    setValue(v => !v);
  }, []);

  return [value, toggle];
}

export default useToggle;
```

### 6. `useWindowSize` - 获取窗口大小

`useWindowSize` 是一个用于获取窗口大小的自定义 Hook。

```javascript
import { useState, useEffect } from 'react';

function useWindowSize() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    }

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return windowSize;
}

export default useWindowSize;
```

### 7. `useOnClickOutside` - 点击外部区域检测

`useOnClickOutside` 是一个用于检测点击是否在组件外部的自定义 Hook。

```javascript
import { useEffect } from 'react';

function useOnClickOutside(ref, handler) {
  useEffect(() => {
    const listener = event => {
      if (!ref.current || ref.current.contains(event.target)) {
        return;
      }
      handler(event);
    };

    document.addEventListener('mousedown', listener);
    document.addEventListener('touchstart', listener);

    return () => {
      document.removeEventListener('mousedown', listener);
      document.removeEventListener('touchstart', listener);
    };
  }, [ref, handler]);
}

export default useOnClickOutside;
```

### 8. `useInterval` - 间隔调用

`useInterval` 是一个用于在指定时间间隔内调用函数的自定义 Hook。

```javascript
import { useEffect, useRef } from 'react';

function useInterval(callback, delay) {
  const savedCallback = useRef();

  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);

  useEffect(() => {
    function tick() {
      savedCallback.current();
    }

    if (delay !== null) {
      const id = setInterval(tick, delay);
      return () => clearInterval(id);
    }
  }, [delay]);
}

export default useInterval;
```

### 9. `useAsync` - 处理异步操作

`useAsync` 是一个用于处理异步操作的自定义 Hook。

```javascript
import { useState, useEffect, useCallback } from 'react';

function useAsync(asyncFunction, immediate = true) {
  const [status, setStatus] = useState('idle');
  const [value, setValue] = useState(null);
  const [error, setError] = useState(null);

  const execute = useCallback(() => {
    setStatus('pending');
    setValue(null);
    setError(null);

    return asyncFunction()
      .then(response => {
        setValue(response);
        setStatus('success');
      })
      .catch(error => {
        setError(error);
        setStatus('error');
      });
  }, [asyncFunction]);

  useEffect(() => {
    if (immediate) {
      execute();
    }
  }, [execute, immediate]);

  return { execute, status, value, error };
}

export default useAsync;
```

### 10. `useHover` - 检测元素悬停

`useHover` 是一个用于检测元素是否被悬停的自定义 Hook。

```javascript
import { useState, useRef, useEffect } from 'react';

function useHover() {
  const [hovered, setHovered] = useState(false);
  const ref = useRef(null);

  const handleMouseEnter = () => setHovered(true);
  const handleMouseLeave = () => setHovered(false);

  useEffect(() => {
    const node = ref.current;
    if (node) {
      node.addEventListener('mouseenter', handleMouseEnter);
      node.addEventListener('mouseleave', handleMouseLeave);

      return () => {
        node.removeEventListener('mouseenter', handleMouseEnter);
        node.removeEventListener('mouseleave', handleMouseLeave);
      };
    }
  }, [ref.current]);

  return [ref, hovered];
}

export default useHover;
```

### 11. `useTimeout` - 管理计时器

实现一个 `useTimeout` 自定义 Hook 可以帮助我们在 React 函数组件中方便地管理 `setTimeout` 计时器。这个 Hook 可以使你在组件内部轻松地设置和清除超时计时器。

```javascript
import { useEffect, useRef, useCallback } from 'react';

function useTimeout(callback, delay) {
  const callbackRef = useRef(callback);
  const timeoutRef = useRef();

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  const set = useCallback(() => {
    timeoutRef.current = setTimeout(() => callbackRef.current(), delay);
  }, [delay]);

  const clear = useCallback(() => {
    timeoutRef.current && clearTimeout(timeoutRef.current);
  }, []);

  useEffect(() => {
    set();

    return clear;
  }, [delay, set, clear]);

  return { set, clear };
}

export default useTimeout;
```

#### 使用示例

下面是一个使用 `useTimeout` Hook 的简单示例：在按钮点击后显示一个消息，并在指定的时间后隐藏它。

```javascript
import React, { useState } from 'react';
import useTimeout from './useTimeout';

function App() {
  const [visible, setVisible] = useState(false);
  const { set, clear } = useTimeout(() => setVisible(false), 5000);

  const handleClick = () => {
    setVisible(true);
    set();
  };

  return (
    <div>
      <button onClick={handleClick}>Show Message</button>
      {visible && <div>This message will disappear in 5 seconds</div>}
    </div>
  );
}

export default App;
```

#### 解释

1. **useRef**:
   - `callbackRef` 用于保存传入的回调函数引用。使用 `useRef` 可以避免在每次重新渲染时重新创建函数。
   - `timeoutRef` 用于保存 `setTimeout` 返回的计时器 ID。

2. **useEffect**:
   - 一个 `useEffect` 用于更新 `callbackRef`，确保它始终指向最新的回调函数。
   - 另一个 `useEffect` 用于设置和清除超时计时器。组件挂载时设置计时器，组件卸载或 `delay` 变化时清除计时器。

3. **useCallback**:
   - `set` 函数用于设置计时器，并将其存储在 `timeoutRef` 中。
   - `clear` 函数用于清除计时器。

4. **返回值**:
   - `set` 和 `clear` 函数返回给调用者，让他们可以手动设置或清除计时器。

### 12. `useUpdate` - 强制组件更新

实现一个 `useUpdate` 方法来强制组件重新渲染，可以通过使用一个内部状态的更新来实现。当状态更新时，组件会重新渲染。我们可以利用 React 的 `useState` Hook 来实现这个功能。

```jsx
import { useState, useCallback } from 'react';

function useUpdate() {
  const [, setState] = useState(0);

  // 使用 useCallback 缓存函数，避免不必要的重新渲染
  return useCallback(() => {
    setState(prev => prev + 1);
  }, []);
}

// 示例组件
function ExampleComponent() {
  const forceUpdate = useUpdate();

  return (
    <div>
      <h1>Example Component</h1>
      <button onClick={forceUpdate}>Force Update</button>
    </div>
  );
}

export default ExampleComponent;
```

#### 解释

1. **useState**: 我们使用 `useState` 来创建一个状态变量 `state` 和一个更新函数 `setState`。这个状态的值不重要，可以是任何值，这里我们使用数字（初始化为0）。

2. **useCallback**: 我们使用 `useCallback` 来缓存 `forceUpdate` 函数，避免每次组件重新渲染时都重新创建该函数。`setState` 调用会更新状态，从而触发组件重新渲染。

3. **forceUpdate**: 我们定义了一个 `forceUpdate` 函数，当它被调用时，会通过 `setState(prev => prev + 1)` 更新状态。每次状态更新都会强制组件重新渲染。

#### 使用示例

你可以在任何函数组件中使用 `useUpdate` 来强制组件重新渲染。以下是一个完整的示例组件：

```jsx
import React from 'react';
import { useState, useCallback } from 'react';

// useUpdate Hook
function useUpdate() {
  const [, setState] = useState(0);
  return useCallback(() => setState(prev => prev + 1), []);
}

// 示例组件
function ExampleComponent() {
  const [count, setCount] = useState(0);
  const forceUpdate = useUpdate();

  return (
    <div>
      <h1>Example Component</h1>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <button onClick={forceUpdate}>Force Update</button>
    </div>
  );
}

export default ExampleComponent;
```

在这个示例中，我们有一个 `count` 状态和一个 `forceUpdate` 方法。点击 "Increment Count" 按钮时，`count` 增加；点击 "Force Update" 按钮时，组件无条件重新渲染。


### 总结

自定义 Hook 是 React 中重用状态逻辑的强大工具。通过自定义 Hook，你可以将组件中重复的逻辑抽取出来，并在多个组件中共享。