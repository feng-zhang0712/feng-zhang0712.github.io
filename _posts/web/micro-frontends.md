# 微前端

微前端（Micro Frontends）是一种架构模式，它将前端应用拆分为多个更小的、独立的子应用，每个子应用可以独立开发、部署和维护。微前端借鉴了微服务在后端的设计思想，将大型单体前端应用拆分为多个小型、松耦合的前端服务，从而提高开发效率、可维护性和可扩展性。

## 一、应用隔离

## 二、微前端概念

## 三、微前端的关键特点

- **独立开发和部署**：每个子应用可以由独立的团队使用不同的技术栈进行开发和部署。
- **技术栈无关**：不同的子应用可以使用不同的前端框架和技术栈（如 React、Vue、Angular 等）。
- **渐进升级和迁移**：可以逐步将旧的单体应用拆分为微前端应用，支持渐进式升级和迁移。
- **独立运行和隔离**：每个子应用在运行时彼此隔离，避免全局变量和样式冲突。

## 四、实现微前端的技术方案

### 4.1 Iframe

Iframe 是一种最简单的实现微前端的方法。每个子应用在独立的 Iframe 中运行，浏览器提供了天然的隔离机制，确保各子应用之间互不干扰。

- 优点：简单易用，不需要复杂的配置。天然隔离，安全性高。
- 缺点：性能较差，每次切换子应用时需要重新加载整个Iframe。用户体验不佳，Iframe内容与主页面的交互性差。

```html
<iframe src="https://example.com/micro-app1" width="100%" height="100%"></iframe>
```

### 4.2 Web Components

Web Components 是一种浏览器原生支持的技术，通过自定义元素和影子 DOM 实现组件的封装和隔离。每个子应用可以作为一个 Web Component 来加载和运行。

- 优点：样式和行为隔离，避免冲突。原生支持，不依赖外部库。
- 缺点：开发复杂度较高。浏览器兼容性问题，需要 Polyfill 支持。

```javascript
class MicroApp extends HTMLElement {
  constructor() {
    super();
    const shadow = this.attachShadow({ mode: 'open' });
    shadow.innerHTML = `
      <style>
        /* 组件内部的样式 */
      </style>
      <div>Micro Frontend Content</div>
    `;
  }
}

customElements.define('micro-app', MicroApp);
```

```html
<micro-app></micro-app>
```

### 4.3 Module Federation（模块联邦）

Module Federation 是 Webpack 5 引入的一项新特性，允许多个独立构建的应用共享和加载各自的模块。通过模块联邦，不同的子应用可以共享代码和依赖，实现更高效的资源利用。

- 优点：高效的模块共享和加载。支持动态加载模块，提升性能。
- 缺点：需要Webpack 5的支持。配置复杂度较高。

```javascript
// 主应用配置
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'main_app',
      remotes: {
        micro_app1: 'micro_app1@http://localhost:3001/remoteEntry.js',
      },
    }),
  ],
};

// 微应用配置
module.exports = {
  plugins: [
    new ModuleFederationPlugin({
      name: 'micro_app1',
      filename: 'remoteEntry.js',
      exposes: {
        './Component': './src/Component',
      },
    }),
  ],
};
```

### 4.4 单页应用框架（Single SPA）

Single SPA 是一个专门用于微前端架构的框架，支持将多个前端框架和库结合在一起运行。它提供了子应用的注册、加载、卸载等生命周期管理，简化了微前端的实现。

- 优点：支持多框架组合，灵活性高。提供丰富的生命周期管理和工具支持。
- 缺点：学习曲线较陡，需要一定的配置和调整。可能需要对现有应用进行改造。

```javascript
import { registerApplication, start } from 'single-spa';

// 注册子应用
registerApplication({
  name: 'micro-app1',
  app: () => import('micro-app1'),
  activeWhen: ['/app1'],
});

// 启动Single SPA
start();
```

### 4.5 Qiankun

Qiankun 是一个基于 Single SPA 的微前端框架，提供了更易用的 API 和更强大的功能，封装了子应用的加载、卸载、通信等常见操作，简化了微前端的开发。

- 优点：基于业界成熟的 Single SPA 架构，稳定性高。易用性强，提供丰富的API和工具支持。
- 缺点：依赖于 Single SPA，需要额外学习和配置。

```javascript
import { registerMicroApps, start } from 'qiankun';

// 注册子应用
registerMicroApps([
  {
    name: 'micro-app1',
    entry: '//localhost:7100',
    container: '#container',
    activeRule: '/app1',
  },
  {
    name: 'micro-app2',
    entry: '//localhost:7200',
    container: '#container',
    activeRule: '/app2',
  },
]);

// 启动Qiankun
start();
```
