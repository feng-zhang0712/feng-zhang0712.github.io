# 前端路由

前端路由（Front-end Routing）是一种在单页应用程序（SPA，Single Page Application）中管理不同页面或视图的方法。它允许应用在不重新加载整个页面的情况下更新 URL，并根据 URL 的变化动态地渲染不同的组件或视图。前端路由通常依赖于浏览器的 `History API` 或 `Hash` 来实现。

## 一、SPA 与前端路由

## 二、前端路由的实现方式

前端路由主要有两种实现方式：基于 `hash` 的路由和基于 `history` 的路由。

### 2.1 基于 Hash 的路由

基于 `hash` 的路由使用 URL 中的 `#` 符号来表示不同的路由。浏览器不会将 `#` 后面的内容发送到服务器，这使得这种方式非常适合前端路由。

**优点**：简单易用，不需要服务器配置。并且兼容性好，几乎所有浏览器都支持。
**缺点**：此方法会导致 URL 锚点失效，此外，可能对 SEO 不友好。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hash Routing Example</title>
</head>
<body>
  <nav>
    <a href="#/">Home</a>
    <a href="#/about">About</a>
    <a href="#/contact">Contact</a>
  </nav>
  <div id="view"></div>
  <script>
    const routes = {
      '/': 'Home Page',
      '/about': 'About Page',
      '/contact': 'Contact Page'
    };

    function router() {
      const hash = location.hash || '#/';
      const route = hash.slice(1);
      document.getElementById('view').innerText = routes[route] || '404 Not Found';
    }

    window.addEventListener('hashchange', router);
    window.addEventListener('load', router);
  </script>
</body>
</html>
```

### 2.2 基于History的路由

基于 `history` 的路由利用 HTML5 的 `History API`（`pushState`、`replaceState` 和 `popstate` 事件）来管理 URL。

**优点**：URL更美观。更符合 SEO 要求（结合服务器端渲染）。
**缺点**：需要服务器配置，确保所有请求都指向同一个 HTML 文件。兼容性稍差，较老的浏览器可能不支持。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>History Routing Example</title>
</head>
<body>
  <nav>
    <a href="/" data-link>Home</a>
    <a href="/about" data-link>About</a>
    <a href="/contact" data-link>Contact</a>
  </nav>
  <div id="view"></div>
  <script>
    const routes = {
      '/': 'Home Page',
      '/about': 'About Page',
      '/contact': 'Contact Page'
    };

    function router() {
      const path = location.pathname;
      document.getElementById('view').innerText = routes[path] || '404 Not Found';
    }

    window.addEventListener('popstate', router);

    document.body.addEventListener('click', e => {
      if (e.target.matches('[data-link]')) {
        e.preventDefault();
        history.pushState(null, '', e.target.href);
        router();
      }
    });

    window.addEventListener('load', router);
  </script>
</body>
</html>
```

## 三、React Router

为了简化前端路由的实现，很多前端框架和库提供了内置的路由解决方案。例如：React Router。

### React Router 实现原理

## 四、前端路由的优缺点

- 用户体验好：前端路由允许在不刷新整个页面的情况下进行视图切换，提供更流畅的用户体验。并且由于不需要从服务器获取整个页面，页面切换速度更快。
- 开发效率高：可以将应用分成多个模块，每个模块对应一个路由，方便管理和维护。不同路由可以共享组件，提高代码复用性。
- 状态管理：页面URL可以用来保持和恢复应用状态，使得浏览器的前进和后退按钮可以正常工作。
- SEO优化：通过结合服务器端渲染，可以在首次加载时提供完整的HTML，提高SEO效果。

但是，前端路由也有缺点。

- 初始加载时间：SPA 通常需要加载大量的 JavaScript 代码，可能导致初始加载时间较长。
- SEO 问题：传统的 SPA 由于没有完整的 HTML，可能会影响 SEO。不过，这个问题可以通过服务器端渲染（SSR）或静态站点生成（SSG）来解决。
- 复杂性：对于大型应用，管理路由和状态可能变得复杂，增加开发和维护的难度。

参考

- [「前端进阶」彻底弄懂前端路由](https://juejin.cn/post/6844903890278694919)
