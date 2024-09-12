`useEffect` 本身不支持直接使用 `async/await`，因为 `useEffect` 的回调函数需要返回一个清理函数或 `undefined`，而 `async` 函数返回的是一个 `Promise`。不过，你可以在 `useEffect` 中定义一个内部的 `async` 函数来处理异步逻辑。

为了简化这种模式，你可以创建一个自定义的 `useAsyncEffect` hook。下面是一个简单的实现：

### 使用 `useEffect` 支持 `async/await`

使用 `useEffect` 支持 `async/await` 的基本模式如下：

```jsx
import React, { useEffect, useState } from 'react';

const MyComponent = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('https://api.example.com/data');
        const result = await response.json();
        setData(result);
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      {data ? <pre>{JSON.stringify(data, null, 2)}</pre> : 'Loading...'}
    </div>
  );
};

export default MyComponent;
```

### 自定义 `useAsyncEffect` Hook

你可以创建一个自定义的 `useAsyncEffect` Hook，使其更简洁优雅。下面是一个简单的实现：

```jsx
import { useEffect } from 'react';

/**
 * useAsyncEffect hook
 * @param {Function} effect - The async function to run in the effect.
 * @param {Array} dependencies - The dependencies array.
 */
const useAsyncEffect = (effect, dependencies = []) => {
  useEffect(() => {
    const asyncEffect = async () => {
      try {
        await effect();
      } catch (error) {
        console.error('useAsyncEffect error:', error);
      }
    };

    asyncEffect();
  }, dependencies);
};

export default useAsyncEffect;
```

### 使用自定义的 `useAsyncEffect` Hook

下面是使用自定义的 `useAsyncEffect` 的示例组件：

```jsx
import React, { useState } from 'react';
import useAsyncEffect from './useAsyncEffect';

const MyComponent = () => {
  const [data, setData] = useState(null);

  useAsyncEffect(async () => {
    const response = await fetch('https://api.example.com/data');
    const result = await response.json();
    setData(result);
  }, []);

  return (
    <div>
      {data ? <pre>{JSON.stringify(data, null, 2)}</pre> : 'Loading...'}
    </div>
  );
};

export default MyComponent;
```

### 处理清理函数

如果你的 `async` 函数需要处理清理逻辑，可以将清理逻辑放在 `useEffect` 的返回函数中。这里是一个更复杂的 `useAsyncEffect` 实现，支持清理函数：

```jsx
import { useEffect } from 'react';

/**
 * useAsyncEffect hook
 * @param {Function} effect - The async function to run in the effect.
 * @param {Array} dependencies - The dependencies array.
 */
const useAsyncEffect = (effect, dependencies = []) => {
  useEffect(() => {
    let isMounted = true;
    
    const asyncEffect = async () => {
      try {
        await effect();
      } catch (error) {
        if (isMounted) {
          console.error('useAsyncEffect error:', error);
        }
      }
    };

    asyncEffect();

    return () => {
      isMounted = false;
    };
  }, dependencies);
};

export default useAsyncEffect;
```

### 示例：处理清理逻辑

```jsx
import React, { useState } from 'react';
import useAsyncEffect from './useAsyncEffect';

const MyComponent = () => {
  const [data, setData] = useState(null);

  useAsyncEffect(async () => {
    const controller = new AbortController();
    const signal = controller.signal;

    try {
      const response = await fetch('https://api.example.com/data', { signal });
      const result = await response.json();
      setData(result);
    } catch (error) {
      if (error.name !== 'AbortError') {
        console.error('Fetch error:', error);
      }
    }

    return () => {
      controller.abort();
    };
  }, []);

  return (
    <div>
      {data ? <pre>{JSON.stringify(data, null, 2)}</pre> : 'Loading...'}
    </div>
  );
};

export default MyComponent;
```

通过自定义 `useAsyncEffect` Hook，可以方便地在 `useEffect` 中使用 `async/await`，并且能够处理清理逻辑，使代码更加简洁和易读。