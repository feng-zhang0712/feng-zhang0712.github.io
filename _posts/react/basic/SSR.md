# 服务端渲染

## 一、客户端渲染（CSR）

## 二、服务端渲染（SSR）

服务端渲染（Server-Side Rendering, SSR）是一种在服务端生成 HTML 内容的技术。与客户端渲染（Client-Side Rendering, CSR）不同，SSR 会在服务器上预先渲染页面并将完整的 HTML 内容发送到客户端。这样，当用户请求页面时，浏览器可以立即显示内容，而不需要等待 JavaScript 在客户端下载和执行。

在 React 中，SSR 的基本原理是，当用户请求页面时，服务器会使用 React 组件生成 HTML 内容，然后将其发送到客户端。客户端接收到 HTML 内容后，React 会在浏览器中 "接管" 这些内容，并将其转化为可交互的 React 组件。

**优点：**

- **更快的首屏渲染速度**：SSR 可以减少首屏渲染的时间，因为 HTML 已经在服务器端生成并发送到客户端。
- **更好的 SEO**：搜索引擎可以更好地抓取和索引预渲染的 HTML 内容，从而提高 SEO 性能。
- **减少客户端工作负载**：由于部分渲染工作在服务器端完成，客户端的渲染压力减轻。

**缺点：**

- **服务器负载增加**：SSR 会增加服务器的工作负载，因为服务器需要处理渲染逻辑。
- **开发复杂度增加**：需要处理更多的服务器端逻辑，并确保客户端和服务器端代码的一致性。

SSR 的主要流程包括以下几个部分。

1. 初始化项目：设置基本的项目结构和依赖，包括 Node.js 服务器、React 应用、Redux 和相关工具的安装。

2. 设置服务器端：创建 Express 服务器，并在服务器端进行React组件的渲染。通过获取数据并将其注入到 Redux store 中，然后生成完整的 HTML 页面返回给客户端。

3. 设置客户端：在客户端入口文件中，使用 `hydrate` 方法让 React 接管服务器端渲染的 HTML 页面，并重新绑定事件处理程序。使用从服务器注入的初始状态创建 Redux store。

4. 创建 React 应用和路由配置：使用 React Router 进行路由管理，确保服务器端和客户端使用相同的路由配置。

5. 数据获取：在服务器端渲染之前，获取所有必要的数据，并注入到 Redux store 中。在客户端挂载组件时，检查是否已经有数据，如果没有，则进行数据获取。

6. 状态管理和同构代码：在服务器端和客户端都使用相同的 Redux store 和 reducer，确保状态管理的一致性。通过在服务器端获取数据，并将初始状态注入到 HTML 页面中，客户端在接管页面时可以使用相同的初始状态。

7. 事件绑定：在客户端接管服务器端渲染的 HTML 页面时，React 会重新绑定事件处理程序，确保客户端的交互功能正常。

8. 同构代码的实现：通过在服务器端和客户端都使用相同的 React 组件和路由配置，实现同构代码，确保渲染结果的一致性、代码的复用性和 SEO 友好性。

下面进行详细介绍。

## 三、React 服务端渲染的实现

示例项目结构如下。

```plaintext
my-ssr-app
├── public
│   └── index.html
├── src
│   ├── client.js
│   ├── server.js
│   ├── App.js
│   ├── routes.js
│   ├── store.js
│   └── components
│       └── MyComponent.js
├── package.json
└── webpack.config.js
```

### 3.1 设置服务器端

设置 Express 服务器，并配置 React 的 SSR。

```javascript
// src/store.js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const createStoreWithMiddleware = () => {
  return createStore(rootReducer, applyMiddleware(thunk));
};

export default createStoreWithMiddleware;
```

```javascript
// src/server.js
import express from 'express';
import React from 'react';
import { renderToString } from 'react-dom/server';
import { StaticRouter } from 'react-router-dom/server';
import { Provider } from 'react-redux';
import App from './App';
import createStore from './store';

const app = express();

app.use(express.static('public'));

// 数据获取函数
const fetchDataForRender = async (req, store) => {
  // 在此处实现根据路由获取数据的逻辑
  // 例如：
  // if (req.path === '/some-path') {
  //   await store.dispatch(someAsyncAction());
  // }
};

app.get('*', async (req, res) => {
  const context = {};
  
  // 创建Redux store
  const store = createStore();

  // 获取数据
  await fetchDataForRender(req, store);

  // 渲染组件为字符串
  const content = renderToString(
    <Provider store={store}>
      <StaticRouter location={req.url} context={context}>
        <App />
      </StaticRouter>
    </Provider>
  );

  // 获取初始状态
  const preloadedState = store.getState();

  // 生成完整的HTML页面
  const html = `
    <!DOCTYPE html>
    <html>
      <head>
        <title>SSR with React</title>
      </head>
      <body>
        <div id="root">${content}</div>
        <script>
          window.__PRELOADED_STATE__ = ${JSON.stringify(preloadedState).replace(/</g, '\\u003c')};
        </script>
        <script src="/bundle.js"></script>
      </body>
    </html>
  `;

  res.send(html);
});

app.listen(3000, () => {
  console.log('Server is listening on port 3000');
});
```

### 3.2 设置客户端

```javascript
// src/components/MyComponent.js
import React from 'react';
import { connect } from 'react-redux';

const MyComponent = ({ someData }) => (
  <div>
    <h1>My Component</h1>
    <p>{someData}</p>
  </div>
);

const mapStateToProps = (state) => ({
  someData: state.someData,
});

export default connect(mapStateToProps)(MyComponent);
```

```javascript
// src/App.js
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import MyComponent from './components/MyComponent';

const App = () => (
  <Routes>
    <Route path="/" element={<MyComponent />} />
    {/* 其他路由 */}
  </Routes>
);

export default App;
```

```javascript
// src/client.js
import React from 'react';
import { hydrate } from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import App from './App';
import createStore from './store';

// 创建Redux store，并使用服务器端的初始状态
const preloadedState = window.__PRELOADED_STATE__;
const store = createStore(preloadedState);

hydrate(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);
```

### 3.3 配置 Webpack

```javascript
// webpack.config.js
const path = require('path');

module.exports = {
  entry: './src/client.js',
  output: {
    path: path.resolve(__dirname, 'public'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env', '@babel/preset-react'],
          },
        },
      },
    ],
  },
};
```

```json
// .babelrc
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

### 3.4 数据获取

在服务端渲染过程中，数据获取是一个关键步骤。我们需要在服务器端渲染 React 组件之前，获取所有必要的数据。

在 `src/server.js` 中的 `fetchDataForRender` 函数中实现。

```javascript
const fetchDataForRender = async (req, store) => {
  // 示例：根据路由获取数据
  const promises = [];
  if (req.path === '/some-path') {
    promises.push(store.dispatch(someAsyncAction()));
  }
  await Promise.all(promises);
};
```

在客户端，数据获取通常在组件的生命周期方法中进行。例如在 `componentDidMount` 中。

```javascript
class MyComponent extends React.Component {
  componentDidMount() {
    if (!this.props.data) {
      this.props.fetchData();
    }
  }
  
  render() {
    // 渲染逻辑
  }
}
```

### 3.5 同构代码实现

在 React SSR 中，同构代码（Isomorphic Code）的实现意味着相同的 React 组件代码可以在服务器端和客户端运行。这带来了多种好处。

- **一致性**：确保服务器端和客户端渲染结果一致。
- **代码复用**：减少重复代码，提高开发效率。
- **SEO友好**：服务器端渲染可以生成完整的HTML页面，有助于搜索引擎优化。

在上面的示例中，我们的 React 组件（如 `App` 和 `MyComponent`）在服务器端和客户端都被使用。这就是同构代码的一种实现方式。

### 3.6 事件绑定

在客户端接管服务器端渲染的 HTML 时，React 会重新绑定事件处理程序。这一步称为 **hydration**。在 `src/client.js` 中的 `hydrate` 方法就是实现这一过程的代码。

```javascript
hydrate(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);
```

### 3.7 路由管理

在 React SSR 中，路由管理需要在服务器端和客户端都进行设置。

- **服务器端**：使用 `StaticRouter` 进行渲染，并传递 `location` 和 `context`。
- **客户端**：使用 `BrowserRouter` 进行渲染。

在 `src/routes.js` 中配置路由。

```javascript
import React from 'react';
import { Route, Routes } from 'react-router-dom';
import MyComponent from './components/MyComponent';

const AppRoutes = () => (
  <Routes>
    <Route path="/" element={<MyComponent />} />
    {/* 其他路由 */}
  </Routes>
);

export default AppRoutes;
```

在 `src/App.js` 中使用路由。

```javascript
import React from 'react';
import AppRoutes from './routes';

const App = () => <AppRoutes />;

export default App;
```

### 3.8 Redux集成和状态管理

在 React SSR 中，Redux 的集成和状态管理需要在服务器端和客户端都进行设置。

- **服务器端**：创建 Redux store，并在渲染前将初始状态注入到 HTML中。
- **客户端**：从服务器注入的初始状态创建 Redux store，并进行 **hydration**。

服务器端集成。

在 `src/server.js` 中创建 Redux store，并在渲染前将初始状态注入到 HTML 中。

```javascript
import createStore from './store';

// 创建Redux store
const store = createStore();

// 获取数据
await fetchDataForRender(req, store);

// 渲染组件为字符串
const content = ReactDOMServer.renderToString(
  <Provider store={store}>
    <StaticRouter location={req.url} context={context}>
      <App />
    </StaticRouter>
  </Provider>
);

// 获取初始状态
const preloadedState = store.getState();

// 生成完整的HTML页面
const html = `
  <!DOCTYPE html>
  <html>
    <head>
      <title>SSR with React</title>
    </head>
    <body>
      <div id="root">${content}</div>
      <script>
        window.__PRELOADED_STATE__ = ${JSON.stringify(preloadedState).replace(/</g, '\\u003c')};
      </script>
      <script src="/bundle.js"></script>
    </body>
  </html>
`;

res.send(html);
```

客户端集成。

在 `src/client.js` 中，从服务器注入的初始状态创建Redux store，并进行“hydration”。

```javascript
import React from 'react';
import { hydrate } from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import App from './App';
import createStore from './store';

// 从服务器注入的初始状态创建Redux store
const preloadedState = window.__PRELOADED_STATE__;
const store = createStore(preloadedState);

hydrate(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);
```

创建Redux Store（`src/store.js`）。

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const createStoreWithMiddleware = (initialState) => {
  return createStore(rootReducer, initialState, applyMiddleware(thunk));
};

export default createStoreWithMiddleware;
```

## 四、为什么要进行同构？

同构（Isomorphic）代码在React SSR中扮演了重要角色，带来了多种好处：

- **一致性**：在服务器端和客户端运行同一份代码，确保渲染结果一致，避免出现不一致的问题。
- **代码复用**：将相同的组件和逻辑在服务器端和客户端复用，减少代码重复，提高开发效率。
- **SEO友好**：服务器端渲染生成的HTML页面包含了完整的内容，这对搜索引擎优化（SEO）非常有利，提高了页面的搜索引擎排名。
- **性能优化**：首次加载时，用户可以直接看到服务器端生成的HTML内容，减少了白屏时间，提高了用户体验。

### 4.1 如何实现同构？

在React SSR中，实现同构代码需要在服务器端和客户端都使用相同的React组件和路由配置。以下是实现同构的一些关键步骤：

#### （1）在服务器端渲染React组件

在服务器端，我们使用 `StaticRouter` 进行渲染，并传递 `location` 和 `context`。

#### （2）在客户端接管服务器端渲染的HTML

在客户端，我们使用 `hydrate` 方法让React接管服务器端渲染的HTML页面，并重新绑定事件处理程序。

#### （3）使用Redux进行状态管理

在服务器端和客户端都使用相同的Redux store和reducer。通过在服务器端获取数据，并将初始状态注入到HTML页面中，客户端在接管页面时可以使用相同的初始状态。

#### （4）路由管理

在服务器端和客户端都使用相同的路由配置。服务器端使用 `StaticRouter` 进行渲染，客户端使用 `BrowserRouter` 进行渲染。

服务器端代码（`src/server.js`）。

```javascript
import express from 'express';
import React from 'react';
import ReactDOMServer from 'react-dom/server';
import { StaticRouter } from 'react-router-dom/server';
import { Provider } from 'react-redux';
import App from './App';
import createStore from './store';

const app = express();

app.use(express.static('public'));

const fetchDataForRender = async (req, store) => {
  const promises = [];
  if (req.path === '/some-path') {
    promises.push(store.dispatch(someAsyncAction()));
  }
  await Promise.all(promises);
};

app.get('*', async (req, res) => {
  const context = {};
  const store = createStore();
  await fetchDataForRender(req, store);

  const content = ReactDOMServer.renderToString(
    <Provider store={store}>
      <StaticRouter location={req.url} context={context}>
        <App />
      </StaticRouter>
    </Provider>
  );

  const preloadedState = store.getState();

  const html = `
    <!DOCTYPE html>
    <html>
      <head>
        <title>SSR with React</title>
      </head>
      <body>
        <div id="root">${content}</div>
        <script>
          window.__PRELOADED_STATE__ = ${JSON.stringify(preloadedState).replace(/</g, '\\u003c')};
        </script>
        <script src="/bundle.js"></script>
      </body>
    </html>
  `;

  res.send(html);
});

app.listen(3000, () => {
  console.log('Server is listening on port 3000');
});
```

客户端代码（`src/client.js`）。

```javascript
import React from 'react';
import { hydrate } from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import App from './App';
import createStore from './store';

const preloadedState = window.__PRELOADED_STATE__;
const store = createStore(preloadedState);

hydrate(
  <Provider store={store}>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </Provider>,
  document.getElementById('root')
);
```

React应用（`src/App.js`）。

```javascript
import React from 'react';
import AppRoutes from './routes';

const App = () => <AppRoutes />;

export default App;
```

路由配置（`src/routes.js`）。

```javascript
import React from 'react';
import { Route, Routes } from 'react-router-dom';
import MyComponent from './components/MyComponent';

const AppRoutes = () => (
  <Routes>
    <Route path="/" element={<MyComponent />} />
    {/* 其他路由 */}
  </Routes>
);

export default AppRoutes;
```

Redux Store（`src/store.js`）。

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const createStoreWithMiddleware = (initialState) => {
  return createStore(rootReducer, initialState, applyMiddleware(thunk));
};

export default createStoreWithMiddleware;
```

Redux Reducer（`src/reducers/index.js`）。

```javascript
import { combineReducers } from 'redux';
import someReducer from './someReducer';

const rootReducer = combineReducers({
  someData: someReducer
});

export default rootReducer;
```

Redux Action（`src/actions/index.js`）。

```javascript
export const FETCH_DATA_SUCCESS = 'FETCH_DATA_SUCCESS';

const fetchDataSuccess = (data) => ({
  type: FETCH_DATA_SUCCESS,
  payload: data
});

export const fetchData = () => {
  return async (dispatch) => {
    const response = await fetch('/api/data');
    const data = await response.json();
    dispatch(fetchDataSuccess(data));
  };
};
```

Redux Reducer 示例（`src/reducers/someReducer.js`）。

```javascript
import { FETCH_DATA_SUCCESS } from '../actions';

const initialState = {};

const someReducer = (state = initialState, action) => {
  switch (action.type) {
    case FETCH_DATA_SUCCESS:
      return {
        ...state,
        data: action.payload
      };
    default:
      return state;
  }
};

export default someReducer;
```

组件示例（`src/components/MyComponent.js`）。

```javascript
import React, { useEffect } from 'react';
import { connect } from 'react-redux';
import { fetchData } from '../actions';

const MyComponent = ({ data, fetchData }) => {
  useEffect(() => {
    if (!data) {
      fetchData();
    }
  }, [data, fetchData]);

  return (
    <div>
      <h1>My Component</h1>
      <p>{data}</p>
    </div>
  );
};

const mapStateToProps = (state) => ({
  data: state.someData.data
});

const mapDispatchToProps = {
  fetchData
};

export default connect(mapStateToProps, mapDispatchToProps)(MyComponent);
```

在上面的代码中，`MyComponent` 组件在挂载后会检查是否已经有数据，如果没有，则调用 `fetchData` 函数来获取数据。
