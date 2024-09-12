# React 项目优化

## 一、React 常见的优化措施

### 1.1 使用 React.memo 和 React.PureComponent 优化组件渲染

(1) **React.memo** 是一个高阶组件，用于优化函数组件的性能。它通过对比前后的 `props`，只有当 `props` 发生变化时才会重新渲染组件。

  ```jsx
  import React, { memo } from 'react';

  const MyComponent = memo(({ someProp }) => {
    return <div>{someProp}</div>;
  });
  ```

(2) **React.PureComponent** 是一个优化工具，它会对比前后的 `props` 和 `state`，只有在它们改变时才会重新渲染组件。

  ```jsx
  class MyComponent extends React.PureComponent {
    render() {
      return <div>{this.props.someProp}</div>;
    }
  }
```

### 1.2 使用 useCallback 和 useMemo

**useCallback** 和 **useMemo** 是 React 中的两个 Hook，用于优化函数和计算结果的重新创建。

(1) **useCallback**：返回一个 memoized（记忆化的）回调函数，避免每次渲染时都创建新的函数实例。

  ```jsx
  const memoizedCallback = useCallback(() => {
    doSomething(a, b);
  }, [a, b]);
  ```

(2) **useMemo**：返回一个 memoized 值，避免每次渲染时都重新计算值。

  ```jsx
  const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
  ```

### 1.3 使用 React 的懒加载（Lazy Loading）

通过 **React.lazy** 和 **Suspense** 实现组件的懒加载，减少初始加载时间。

```jsx
import React, { lazy, Suspense } from 'react';

const LazyComponent = lazy(() => import('./LazyComponent'));

function MyComponent() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <LazyComponent />
    </Suspense>
  );
}
```

### 1.4 避免不必要的渲染

确保组件只在必要时渲染，避免不必要的更新。

(1) **条件渲染**：只在需要时渲染组件。

  ```jsx
  {shouldRender && <MyComponent />}
  ```

(1) **控制组件的 key**：确保列表组件的 `key` 是唯一的且稳定的，以避免不必要的重新渲染。

  ```jsx
  {items.map(item => <ListItem key={item.id} item={item} />)}
  ```

### 1.5 避免匿名函数和内联对象

在渲染中避免使用匿名函数和内联对象，它们会在每次渲染时创建新的实例，导致不必要的重新渲染。

```jsx
// 不推荐
<button onClick={() => doSomething()}>Click me</button>

// 推荐
const handleClick = useCallback(() => {
  doSomething();
}, []);
<button onClick={handleClick}>Click me</button>
```

### 1.6 使用 Virtual DOM

React 本身使用虚拟 DOM（Virtual DOM）来提高性能，但在大型应用中，可以结合使用 **virtualization** 技术（如 react-window 或 react-virtualized）来优化长列表的渲染。

```jsx
import { FixedSizeList as List } from 'react-window';

const Row = ({ index, style }) => (
  <div style={style}>
    Row {index}
  </div>
);

const Example = () => (
  <List
    height={150}
    itemCount={1000}
    itemSize={35}
    width={300}
  >
    {Row}
  </List>
);
```

### 1.7 异步数据加载

- **Debounce** 和 **Throttle**：在处理用户输入时，使用 debounce 和 throttle 减少不必要的请求和计算。
- **Suspense for Data Fetching**：使用 React 未来的特性，如 Suspense for Data Fetching 以更高效地加载数据。

### 1.8 服务端渲染（Server-Side Rendering, SSR）

使用 Next.js 等框架实现服务端渲染，减少初始加载时间，提高 SEO 和首屏渲染速度。

### 1.9 CSS 优化

- **CSS-in-JS**：使用 CSS-in-JS 库（如 styled-components）可以避免不必要的重绘和重排。
- **Critical CSS**：将关键 CSS 提取到 HTML 中，减少初始渲染时间。

### 1.10 使用性能优化工具

React 的 Profiler 是 React 16 版本中引入的一个开发工具，用于帮助开发者分析和优化 React 应用的性能。它允许你在应用中测量渲染时间和识别性能瓶颈，尤其是在组件树中哪些组件的渲染时间较长或渲染频率较高。Profiler 的使用和分析可以帮助优化 React 应用的性能。

#### (1) 概述

Profiler的主要功能是：

- 记录每个渲染周期中组件树的“渲染时间”。
- 提供每个组件和子树的渲染时间、渲染次数等信息。
- 帮助识别性能瓶颈和不必要的渲染。

#### (2) 使用方法

要使用 Profiler，需要将其包裹在你想要分析的组件树中。Profiler 组件接受一个 `id` 和一个 `onRender` 回调函数作为props。

```jsx
import React, { Profiler } from 'react';

const onRenderCallback = (
  id, // Profiler 树的 id
  phase, // "mount" 或 "update"
  actualDuration, // 本次更新所花费的时间
  baseDuration, // 理想情况下渲染整个子树所需的时间
  startTime, // 本次更新开始的时间
  commitTime, // 本次更新结束的时间
  interactions // 本次更新的交互
) => {
  console.log({
    id,
    phase,
    actualDuration,
    baseDuration,
    startTime,
    commitTime,
    interactions,
  });
};

const App = () => (
  <Profiler id="App" onRender={onRenderCallback}>
    <MyComponent />
  </Profiler>
);

export default App;
```

- `id`：Profiler树的ID，用于标识不同的Profiler实例。
- `phase`：表示当前渲染是“mount”还是“update”。
- `actualDuration`：本次更新花费的时间。
- `baseDuration`：在没有memoization（记忆化）优化的情况下，渲染整个子树所需的时间。
- `startTime`：本次更新开始的时间。
- `commitTime`：本次更新结束的时间。
- `interactions`：与本次更新相关的交互。

假设我们有以下组件结构：

```jsx
const ChildComponent = () => {
  return <div>Child Component</div>;
};

const ParentComponent = () => {
  return (
    <div>
      <ChildComponent />
      <ChildComponent />
    </div>
  );
};

const App = () => {
  return (
    <Profiler id="ParentComponent" onRender={onRenderCallback}>
      <ParentComponent />
    </Profiler>
  );
};
```

在这个示例中，`onRenderCallback` 会在 `ParentComponent` 及其子组件每次渲染时被调用。通过分析 `actualDuration` 和 `baseDuration`，我们可以确定是否存在性能问题，例如哪些组件渲染过于频繁或者渲染时间过长。

#### （3）使用React Developer Tools

除了在代码中使用 Profiler 组件，你还可以使用 React Developer Tools 进行更直观的性能分析。

## 二、Webpack 常见的优化措施

### 2.1 代码拆分（Code Splitting）

代码分割（Code Splitting）是优化 React 应用性能的重要技术之一。通过代码分割，可以将应用的代码拆分成多个小块，按需加载，从而减少初始加载时间，提高应用的响应速度和用户体验。

1. **减少初始加载时间**：现代的单页应用（SPA）通常包含大量的 JavaScript 代码。如果将所有代码在页面初次加载时全部加载，会导致加载时间过长，影响用户体验。代码分割可以将应用的代码拆分成多个小块，只有在需要时才加载相应的代码，从而减少初始加载时间，提高页面加载速度。
2. **提高应用的响应速度**：按需加载代码可以减少浏览器需要解析和执行的 JavaScript 代码量，从而提高应用的响应速度。通过代码分割，用户在访问不同页面或功能时，可以更快地加载和渲染相应的内容，提高应用的整体响应速度和用户体验。
3. **优化资源利用**：在大型应用中，不同的功能模块可能不需要同时加载。例如，用户可能只会访问某些特定的页面或功能，而不会访问整个应用的所有部分。代码分割可以确保只有在用户需要时才加载相应的代码，从而优化资源利用，减少不必要的网络请求和资源浪费。
4. **提高代码的可维护性**：将应用的代码拆分成多个小块，可以使代码结构更加清晰和模块化，便于维护和更新。通过代码分割，可以更容易地管理和维护不同的功能模块，减少代码耦合，提高代码的可维护性和可扩展性。

```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all',
    },
  },
};
```

### 2.2 懒加载（Lazy Loading）

懒加载可以确保只在需要时加载模块，减少初始加载时间。

```javascript
// 使用动态导入进行懒加载
import(/* webpackChunkName: "myLazyLoadedModule" */ './myLazyLoadedModule').then(module => {
  module.default();
});
```

### 2.3 Tree Shaking

Tree Shaking 是一种去除未使用代码（dead-code）的技术，可以显著减少打包后的文件大小。确保你的代码使用 ES6 模块，因为 Tree Shaking 依赖于 ES6 模块的静态结构。

```javascript
module.exports = {
  mode: 'production',
  optimization: {
    usedExports: true,
  },
};
```

### 2.4 文件压缩

使用压缩工具进一步减少打包文件的大小。

(1) **CSS 压缩**：使用 `css-minimizer-webpack-plugin` 压缩 CSS 文件。

  ```javascript
  const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

  module.exports = {
    optimization: {
      minimize: true,
      minimizer: [
        '...', // 继承默认的 minimizer
        new CssMinimizerPlugin(),
      ],
    },
  };
  ```

(2) **JavaScript 压缩**：使用 `TerserPlugin` 压缩 JavaScript 文件。

  ```javascript
  const TerserPlugin = require('terser-webpack-plugin');

  module.exports = {
    optimization: {
      minimize: true,
      minimizer: [new TerserPlugin()],
    },
  };
  ```

### 2.5 图片优化

使用 `image-webpack-loader` 或其他图片优化插件，压缩图片文件大小。

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpe?g|gif|svg)$/i,
        use: [
          {
            loader: 'file-loader',
          },
          {
            loader: 'image-webpack-loader',
            options: {
              mozjpeg: {
                progressive: true,
                quality: 65,
              },
              optipng: {
                enabled: false,
              },
              pngquant: {
                quality: [0.65, 0.90],
                speed: 4,
              },
              gifsicle: {
                interlaced: false,
              },
            },
          },
        ],
      },
    ],
  },
};
```

### 2.6 缓存（Caching）

配置缓存可以提高重复访问时的加载速度。

（1） 使用 Content Hash。

```javascript
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
  },
};
```

（2） 使用缓存插件。

```javascript
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  plugins: [
    new CleanWebpackPlugin(),
  ],
};
```

### 2.7 使用 CDN

将静态资源（包括 JavaScript 和 CSS 文件）上传到 CDN，可以显著提高加载速度。

```javascript
module.exports = {
  output: {
    publicPath: 'https://cdn.example.com/assets/', // 替换成你的 CDN 地址
  },
};
```

### 2.8 使用 Webpack 的持久化缓存（Persistent Caching）

Webpack 5 引入了持久化缓存，可以显著减少构建时间。

```javascript
module.exports = {
  cache: {
    type: 'filesystem',
  },
};
```

### 2.9 优化开发体验

(1) **Hot Module Replacement (HMR)**：热模块替换可以在不刷新整个页面的情况下，替换、添加或删除模块。

  ```javascript
  module.exports = {
    devServer: {
      hot: true,
    },
  };
  ```

(2) **使用速度分析工具**：如 `webpack-bundle-analyzer` 分析打包后的文件大小，帮助找到需要优化的地方。

  ```javascript
  const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer');

  module.exports = {
    plugins: [
      new BundleAnalyzerPlugin(),
    ],
  };
  ```

### 2.10 减少 Babel 转译时间

通过配置 `babel-loader`，只转译项目中的文件，排除 `node_modules` 中的文件。

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
        },
      },
    ],
  },
};
```

### 2.11 使用多线程/多进程构建

使用 `thread-loader` 或 `parallel-webpack` 来并行处理构建任务，提高构建速度。

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: [
          'thread-loader',
          'babel-loader',
        ],
      },
    ],
  },
};
```

## 参考

- [读懂前端「性能优化」](https://tech.qimao.com/yi-wen-du-dong-qian-duan-xing-neng-you-hua/)
