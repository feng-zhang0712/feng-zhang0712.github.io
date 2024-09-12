# React Router

React Router 是 React 应用程序中最常用的路由解决方案。它允许你在单页面应用中实现客户端路由，管理 URL 和组件之间的映射关系。

## 一、介绍

### 1.1 React Router 的定义

React Router 是一个专门为 React 应用设计的强大的路由库。它可以让开发者在 React 应用中实现类似传统网页的路由功能，即根据不同的 URL 地址显示不同的组件或页面内容。React Router 使构建单页应用（SPA）变得更加容易，并提供了许多强大的功能。

- **声明式路由：** 使用类似 JSX 的语法定义路由规则，使代码更易读和维护。
- **动态路由匹配：** 可以根据 URL 中的参数动态匹配路由，例如 `/users/:id` 可以匹配 `/users/1`、`/users/2` 等。
- **嵌套路由：** 可以创建具有父子关系的路由结构，使应用更易于组织和管理。
- **编程式导航：** 可以使用 JavaScript 代码控制路由跳转，例如 `history.push('/home')`。
- **路由守卫：** 可以在路由跳转之前或之后执行一些逻辑，例如权限验证、数据加载等。

### 1.2 React Router 与传统路由的区别

传统路由通常由服务器端处理，当用户访问不同的 URL 时，服务器会返回不同的 HTML 页面。而在单页应用（SPA）中，只有一个 HTML 页面，所有的内容都是通过 JavaScript 动态加载和渲染的。

React Router 就是为了解决 SPA 中的路由问题而诞生的。它 **在浏览器端管理路由，通过监听 URL 的变化，动态地渲染不同的组件**，而不是向服务器发送请求。

| 特性         | 传统路由      | React Router   |
|--------------|----------------|----------------|
| 路由处理位置 | 服务器端       | 浏览器端        |
| 页面加载方式  | 重新加载页面   | 动态渲染组件      |
| 用户体验       | 页面跳转明显   | 页面跳转流畅      |

### 1.3 react-router 与 react-router-dom

**react-router** 包提供了 React Router 的核心 API，包括路由的核心逻辑、路由匹配算法、导航控制等。它不依赖于具体的运行环境，可以用于浏览器、Node.js 服务器、React Native 等环境。

**react-router-dom** 包是 **react-router 在 Web 环境下的扩展**，它提供了专门用于 Web 应用的组件和 API。

**简单来说，如果开发的是 Web 应用，就需要安装和使用 `react-router-dom` 包。**

## 二、React Router 组件

### 2.1 Router

**Router** 是 React Router 的核心组件，它 **提供了路由上下文**，使得其子组件能够访问路由相关的信息和功能。常见的 `<Router>` 类型有如下几种。

#### （1）BrowserRouter

**BrowserRouter** 使用的是 HTML5 的 history API（包括 pushState、replaceState 和 popstate 事件）来管理页面的历史栈。因此，URL 看起来更清晰、更简洁。

BrowserRouter 通过 `history.pushState` 和 `history.replaceState` 方法来改变地址栏的 URL 而不重新加载页面。通过监听 `popstate` 事件来检测浏览器导航（如点击后退按钮）并更新 UI。

```jsx
import React from 'react';
import { BrowserRouter, Route, Switch, Link } from 'react-router-dom';

function Home() {
  return <h2>Home</h2>;
}

function About() {
  return <h2>About</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <div>
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/about">About</Link></li>
          </ul>
        </nav>

        <Switch>
          <Route path="/" exact component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </div>
    </BrowserRouter>
  );
}

export default App;
```

#### （2）HashRouter

**HashRouter** 使用的是 URL 的哈希部分（# 后面的部分）来保持页面状态。这种方式不需要服务器支持，因为所有的路由信息都保存在 URL 的哈希部分，浏览器不会向服务器发送请求。

HashRouter 通过 `window.location.hash` 来控制路由，哈希部分的变化不会触发浏览器重新加载页面。通过监听 `hashchange` 事件来检测 URL 哈希的变化并更新 UI。

```jsx
import React from 'react';
import { HashRouter, Route, Switch, Link } from 'react-router-dom';

function Home() {
  return <h2>Home</h2>;
}

function About() {
  return <h2>About</h2>;
}

function App() {
  return (
    <HashRouter>
      <div>
        <nav>
          <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/about">About</Link></li>
          </ul>
        </nav>

        <Switch>
          <Route path="/" exact component={Home} />
          <Route path="/about" component={About} />
        </Switch>
      </div>
    </HashRouter>
  );
}

export default App;
```

#### （3）MemoryRouter

**MemoryRouter** 是一种完全在内存中保存历史记录的路由器。它不会对外部 URL 进行任何修改，适用于测试环境或不需要与浏览器历史记录交互的场景。

#### （4）StaticRouter

**`StaticRouter`** 是一个不监听浏览器上下文的路由器，通常用于服务器端渲染（SSR）。需要手动提供 location 和 context。

```jsx
import React from 'react';
import { StaticRouter, Route, Switch } from 'react-router-dom';

function Home() {
  return <h2>Home</h2>;
}

function About() {
  return <h2>About</h2>;
}

function App({ location, context }) {
  return (
    <StaticRouter location={location} context={context}>
      <Switch>
        <Route path="/" exact component={Home} />
        <Route path="/about" component={About} />
      </Switch>
    </StaticRouter>
  );
}

export default App;
```

### 2.2 Route

`Route` 组件主要用于定义路由规则。它会根据当前 URL 路径匹配对应的组件，并进行渲染。它的主要属性如下。

- `path`：定义路由的路径。当 URL 匹配该路径时，渲染对应的组件。
- `element`：指定当路由匹配时要渲染的组件。
- `exact`：如果为 true，则要求路径完全匹配。否则，部分匹配也会触发路由。
- `render`：可以传递一个函数，当路径匹配时调用这个函数。适用于需要传递额外 props 的场景。
- `children`：无论路径是否匹配，都会渲染其内容。提供路径匹配的信息。

```jsx
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<HomePage />} />
        <Route path="/about" element={<AboutPage />} />
      </Routes>
    </Router>
  );
}
```

在这个例子中，当用户访问根路径 `/` 时，会渲染 `HomePage` 组件；当用户访问 `/about` 路径时，会渲染 `AboutPage` 组件。

### 2.3 Switch

`Switch` 组件用于包装 `Route` 组件，当路径匹配时，只渲染第一个匹配的路由。

```jsx
import { Switch, Route } from 'react-router-dom';

function App() {
  return (
    <Switch>
      <Route path="/home" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/contact" component={Contact} />
    </Switch>
  );
}
```

### 2.4 Link

`Link` 组件用于创建可点击的导航链接，类似于 HTML 中的 `<a>` 标签，但不会刷新页面。

```jsx
import { Link } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <Link to="/home">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  );
}
```

### 2.5 NavLink

`NavLink` 组件是 `Link` 组件的一个变体，用于创建带有活动状态的导航链接。可以通过 `activeClassName` 或 `activeStyle` 属性自定义活动状态的样式。

```jsx
import { NavLink } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <NavLink to="/home" activeClassName="active">Home</NavLink>
      <NavLink to="/about" activeClassName="active">About</NavLink>
      <NavLink to="/contact" activeClassName="active">Contact</NavLink>
    </nav>
  );
}
```

### 2.6 Redirect

`Redirect` 组件用于重定向到指定路径。

```jsx
import { Redirect, Route } from 'react-router-dom';

function App() {
  return (
    <Switch>
      <Route path="/home" component={Home} />
      <Route path="/about" component={About} />
      <Redirect from="/old-about" to="/about" />
    </Switch>
  );
}
```

### 2.7 Prompt

`Prompt` 组件用于阻止导航，通常用于在用户离开页面前确认。

```jsx
import { Prompt } from 'react-router-dom';

function MyComponent() {
  const [isBlocking, setIsBlocking] = useState(false);

  return (
    <div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          setIsBlocking(false);
        }}
        onChange={() => {
          setIsBlocking(true);
        }}
      >
        <Prompt
          when={isBlocking}
          message={(location) => `Are you sure you want to go to ${location.pathname}?`}
        />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

### 2.8 Hooks

#### （1）useHistory

`useHistory` 是 React Router 提供的钩子，用于访问和操纵历史对象。

```jsx
import { useHistory } from 'react-router-dom';

function MyComponent() {
  const history = useHistory();

  const handleClick = () => {
    history.push('/new-path');
  };

  return (
    <button onClick={handleClick}>Go to New Path</button>
  );
}
```

#### （2）useLocation

`useLocation` 钩子返回当前的 location 对象，包含 pathname、search、hash 等信息。

```jsx
import { useLocation } from 'react-router-dom';

function MyComponent() {
  const location = useLocation();

  return (
    <div>
      <p>Current Path: {location.pathname}</p>
    </div>
  );
}
```

#### （3）useParams

`useParams` 钩子返回当前路由匹配的参数对象。

```jsx
import { useParams } from 'react-router-dom';

function MyComponent() {
  const { id } = useParams();

  return (
    <div>
      <p>Parameter ID: {id}</p>
    </div>
  );
}
```

#### （4）seRouteMatch

`useRouteMatch` 钩子返回当前匹配的路由对象，可以用于获取路径、url 等信息。

```jsx
import { useRouteMatch } from 'react-router-dom';

function MyComponent() {
  const match = useRouteMatch('/example-path');

  return (
    <div>
      {match && <p>Matched Path: {match.url}</p>}
    </div>
  );
}
```

## 三、动态路由

## 一个React Router 示例

```plainttext
src/
  ├── components/
  │   ├── AdminPage.js
  │   ├── LoginPage.js
  │   ├── HomePage.js
  │   └── ProtectedRoute.js
  ├── App.js
  ├── auth.js
  └── index.js
```

### 1. 设置身份验证逻辑

在 `auth.js` 中实现简单的身份验证逻辑和用户角色管理：

```javascript
// auth.js
const fakeAuth = {
  isAuthenticated: false,
  userRole: null,
  
  // 模拟登录
  login(role, cb) {
    this.isAuthenticated = true;
    this.userRole = role;
    setTimeout(cb, 100); // 模拟异步回调
  },

  // 模拟登出
  logout(cb) {
    this.isAuthenticated = false;
    this.userRole = null;
    setTimeout(cb, 100);
  },

  // 获取当前用户角色
  getRole() {
    return this.userRole;
  }
};

export default fakeAuth;
```

### 2. 创建受保护的路由组件

在 `ProtectedRoute.js` 中创建一个受保护的路由组件，用于根据用户的身份验证状态和角色来控制路由访问：

```javascript
// components/ProtectedRoute.js
import React from 'react';
import { Route, Redirect } from 'react-router-dom';
import fakeAuth from '../auth';

// 受保护的路由组件
const ProtectedRoute = ({ component: Component, roles, ...rest }) => (
  <Route
    {...rest}
    render={props =>
      fakeAuth.isAuthenticated ? (
        roles.includes(fakeAuth.getRole()) ? (
          <Component {...props} />
        ) : (
          <Redirect to="/unauthorized" />
        )
      ) : (
        <Redirect to="/login" />
      )
    }
  />
);

export default ProtectedRoute;
```

### 3. 创建示例页面组件

在 `components/` 目录下创建一些示例页面组件：

```javascript
// components/HomePage.js
import React from 'react';

const HomePage = () => <h2>Home Page</h2>;

export default HomePage;

// components/AdminPage.js
import React from 'react';

const AdminPage = () => <h2>Admin Page - Admin Access Only</h2>;

export default AdminPage;

// components/LoginPage.js
import React, { useState } from 'react';
import fakeAuth from '../auth';

const LoginPage = ({ history }) => {
  const [role, setRole] = useState('user');

  const login = () => {
    fakeAuth.login(role, () => {
      history.replace('/');
    });
  };

  return (
    <div>
      <h2>Login Page</h2>
      <select value={role} onChange={(e) => setRole(e.target.value)}>
        <option value="user">User</option>
        <option value="admin">Admin</option>
      </select>
      <button onClick={login}>Login</button>
    </div>
  );
};

export default LoginPage;
```

### 4. 配置路由

在 `App.js` 中配置路由，使用 `ProtectedRoute` 来保护需要权限的路由：

```javascript
// App.js
import React from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
import HomePage from './components/HomePage';
import AdminPage from './components/AdminPage';
import LoginPage from './components/LoginPage';
import ProtectedRoute from './components/ProtectedRoute';

const App = () => (
  <Router>
    <Switch>
      <Route exact path="/" component={HomePage} />
      <Route path="/login" component={LoginPage} />
      <ProtectedRoute path="/admin" component={AdminPage} roles={['admin']} />
      <Route path="/unauthorized" render={() => <h2>Unauthorized Access</h2>} />
    </Switch>
  </Router>
);

export default App;
```

### 5. 运行项目

确保入口文件（如 `index.js`）正确渲染 `App` 组件：

```javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

<!-- 基础知识 -->
<!-- 什么是 React Router？
React Router 与传统路由的区别是什么？
React Router 中的 <Router> 和 <Route> 有什么作用？
react-router-dom 和 react-router 有什么区别？ -->
<!-- 路由类型 -->
<!-- 解释一下 BrowserRouter 和 HashRouter 的区别。 -->
<!-- MemoryRouter 用于什么场景？ -->
如何在 React Router 中实现动态路由？
如何在 React Router 中实现嵌套路由？
路由管理与导航
如何在 React Router 中进行重定向？
如何在 React Router 中实现程序化导航（programmatic navigation）？
React Router 中的 Link 和 NavLink 有什么区别？
如何在 React Router 中获取当前的路由信息？
高级用法
解释一下 React Router 中的 Switch 组件的作用及用法。
如何在 React Router 中使用 useParams 钩子？
如何在 React Router 中使用 useHistory 钩子？
如何在 React Router 中使用 useLocation 钩子？
在 React Router 中实现路由守卫的方式有哪些？
场景题目
如何在一个 React 应用中实现基于角色的访问控制？
如何在 React Router 中实现按需加载（代码拆分）？
描述你如何在 React Router 中处理 404 页面。
在 React Router 中如何实现一个带有侧边栏导航的应用？
如何在 React Router 中实现一个多语言（国际化）应用的路由管理？
描述如何在 React Router 中处理表单提交后的导航。
在 React Router 中如何实现一个分页系统？
如何在 React Router 中实现一个带有查询参数（query parameters）的搜索页面？