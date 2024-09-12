## React 中的组件构建方式

在 React 中，构建组件的方式主要有以下几种：

1. **函数组件（Functional Components）**
2. **类组件（Class Components）**
3. **高阶组件（Higher-Order Components，HOC）**
4. **Render Props**
5. **自定义钩子（Custom Hooks）**

每种方式都有其特定的使用场景和优势。下面我们详细介绍每一种方式。

### 1. 函数组件（Functional Components）

函数组件是使用函数定义的组件，它接受 props 并返回 React 元素。自从 React 16.8 引入了 Hooks 后，函数组件可以使用 `useState`、`useEffect` 等 Hooks 来管理状态和副作用，使其几乎完全取代了类组件。

**示例**

```jsx
import React, { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

export default Counter;
```

### 2. 类组件（Class Components）

类组件是使用 ES6 的 class 语法定义的组件，它继承自 `React.Component`。类组件有自己的状态和生命周期方法，但由于其相对复杂且冗长，在 Hooks 出现后逐渐被函数组件取代。

**示例**

```jsx
import React, { Component } from 'react';

class Counter extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}

export default Counter;
```

### 3. 高阶组件（Higher-Order Components，HOC）

高阶组件是一种函数，它接受一个组件并返回一个新的组件。HOC 通常用于复用组件逻辑，例如权限控制、数据获取等。

**示例**

```jsx
import React from 'react';

function withLogging(WrappedComponent) {
  return class extends React.Component {
    componentDidMount() {
      console.log('Component did mount');
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}

class MyComponent extends React.Component {
  render() {
    return <div>Hello, World!</div>;
  }
}

export default withLogging(MyComponent);
```

### 4. Render Props

Render Props 是一种通过一个返回 React 元素的函数来实现组件之间代码共享的技术。它通常用于复用组件逻辑。

**示例**

```jsx
import React, { Component } from 'react';

class MouseTracker extends Component {
  constructor(props) {
    super(props);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove = (event) => {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    });
  };

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <h1>
          The mouse position is ({x}, {y})
        </h1>
      )}
    />
  );
}

export default App;
```

### 5. 自定义钩子（Custom Hooks）

自定义钩子是复用逻辑的另一种方法，它是一个以 `use` 开头的函数，可以调用其他钩子。自定义钩子使得在函数组件中复用逻辑变得简单和优雅。

**示例**

```jsx
import React, { useState, useEffect } from 'react';

function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (event) => {
      setPosition({
        x: event.clientX,
        y: event.clientY,
      });
    };

    window.addEventListener('mousemove', handleMouseMove);

    return () => {
      window.removeEventListener('mousemove', handleMouseMove);
    };
  }, []);

  return position;
}

function MouseTracker() {
  const position = useMousePosition();

  return (
    <div>
      <h1>
        The mouse position is ({position.x}, {position.y})
      </h1>
    </div>
  );
}

export default MouseTracker;
```

### 总结

在 React 中，可以通过函数组件、类组件、高阶组件、Render Props、自定义钩子等多种方式来构建组件。这些方式各有优缺点和适用场景：

- **函数组件**：简单、易读、性能好，结合 Hooks 可以替代大部分类组件的场景。
- **类组件**：适用于需要复杂状态和生命周期管理的场景，但由于相对复杂和冗长，逐渐被函数组件取代。
- **高阶组件**：用于复用组件逻辑，但可能导致嵌套过深和调试困难。
- **Render Props**：用于复用组件逻辑，但可能导致代码不够直观。
- **自定义钩子**：现代 React 推荐的复用逻辑方式，简单、灵活且易于理解。

选择哪种方式取决于你的项目需求和团队技术栈。