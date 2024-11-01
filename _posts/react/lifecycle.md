# React 类组件的生命周期

React 组件生命周期（Lifecycle）指的是组件从创建、更新到销毁的整个过程。React 的组件生命周期可以分为三个主要阶段：挂载（Mounting）、更新（Updating）和卸载（Unmounting）。

## 一、挂载阶段（Mounting）

挂载阶段是组件被创建并插入到 DOM 中的过程。这个阶段包含以下生命周期方法：

### 1.1 **constructor(props)**

构造函数在组件挂载之前调用。通常用于初始化状态和绑定事件处理方法。

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
}
```

### 1.2 **static getDerivedStateFromProps(props, state)**

`getDerivedStateFromProps` 方法在组件实例化后和每次渲染之前调用，无论是父组件的更新、`props` 的变化或通过 `setState` 更新组件内部的 State，它都会被调用。如果返回一个对象，那么这个对象将会被合并到当前的 `state` 中。如果返回 `null`，则表示不需要更新 `state`。

`getDerivedStateFromProps` 的主要使用场景是当组件的 `state` 需要基于 `props` 来更新时。例如，一个父组件传递新的 `props` 给子组件，子组件需要使用这些新的 `props` 来更新其内部状态。

当组件实例化时，该方法替代了 `componentWillMount()`；当接收新的 `props` 时，该方法替代了 `componentWillReceiveProps()` 和 `componentWillUpdate()`。

```javascript
class MyComponent extends React.Component {
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.someValue !== prevState.someValue) {
      return {
        someState: nextProps.someValue
      };
    }
    return null;
  }
}
```

#### 为什么该函数设计成静态方法？

1. 作为静态方法，`getDerivedStateFromProps` 不能访问组件实例的 `this`，这意味着它不能访问组件实例的任何属性和方法。这种设计防止了在这个生命周期方法中引入副作用。
2. 静态方法使得 `getDerivedStateFromProps` 只能接收 `nextProps` 和 `prevState` 作为参数，并返回一个新的 `state` 或 `null`。这使得这个方法的输入输出明确，可预测性更强。
3. 在 React 16.3 之前，`componentWillReceiveProps` 方法用于基于新的 `props` 更新 `state`，但这个方法容易引入副作用，且其行为在异步渲染中并不稳定。`getDerivedStateFromProps` 作为静态方法，引入了更明确的规则，简化了生命周期管理。
4. 静态方法设计有助于 React 在调和（reconciliation）过程中进行性能优化。

#### 注意

- 此方法在组件挂载和更新阶段都会触发。
- 如果父组件导致了组件的重新渲染，即使属性没有更新，此方法也会被触发。
- 如果只想处理 `props` 的前后变化，需要将旧的 `props` 值存到 `state` 里面作为镜像。
- 该函数是一个静态函数，所以函数体内无法访问指向当前组件实例的指针 `this`。
- 当需要更新 `state` 时，需要返回一个对象，否则，返回一个 `null`。

### 1.3 **render()**

`render` 方法是唯一必须实现且用于渲染的纯函数，它返回要渲染的 React 元素。

`render` 方法并不做实际的渲染动作（渲染到 DOM 树），它返回的一个 JSX 的描述对象（及组件实例），何时执行真正的渲染动作由 React 决定。React 会把所有组件的渲染函数返回的结果综合起来，再执行具体的 DOM 渲染操作。

```javascript
class MyComponent extends React.Component {
  render() {
    return <div>{this.state.count}</div>;
  }
}
```

### 1.4 **componentDidMount()**

`componentDidMount` 方法在组件挂载之后立即调用。适用场景如发送网络请求、添加事件监听或者操作 DOM 等。

注意，该函数不会在 `render` 函数调用完成后立即触发，而是在组件被渲染到 DOM 树后才触发。

## 二、更新阶段（Updating）

更新阶段是组件重新渲染的过程，可能是由于 `props` 或 `state` 的变化引起的。这个阶段包含以下生命周期方法：

### 2.1 **static getDerivedStateFromProps(props, state)**

同上，在每次渲染之前调用。

### 2.2 **shouldComponentUpdate(nextProps, nextState)**

`shouldComponentUpdate` 方法在组件接收到新的 `props` 或 `state` 时调用。此方法返回一个布尔值，`true` 表示允许更新，`false` 表示禁止更新。
`shouldComponentUpdate` 方法主要用于性能优化，以避免不必要的渲染。

```javascript
class MyComponent extends React.Component {
  shouldComponentUpdate(nextProps, nextState) {
    return nextState.count !== this.state.count;
  }
}
```

### 2.3 **render()**

同上，渲染方法。

### 2.4 **getSnapshotBeforeUpdate(prevProps, prevState)**

`getSnapshotBeforeUpdate` 方法在最近一次渲染的输出提交给 DOM 之前调用。主要用于捕获一些 DOM 信息（如滚动位置）以备更新后使用。

注意，该函数返回值将作为 `componentDidUpdate()` 的第三个参数。此方法不是常用的，只有在需要处理特定 DOM 更新场景时才使用。

```javascript
class MyComponent extends React.Component {
  getSnapshotBeforeUpdate(prevProps, prevState) {
    if (prevProps.list.length < this.props.list.length) {
      return this.listRef.scrollHeight - this.listRef.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    if (snapshot !== null) {
      this.listRef.scrollTop = this.listRef.scrollHeight - snapshot;
    }
  }
}
```

### 2.5 **componentDidUpdate(prevProps, prevState, snapshot)**

`componentDidUpdate` 方法在组件更新后立即调用。可以使用 `snapshot` 参数来执行一些操作。

注意，应该避免在这方法中直接调用 `setState`，否则会导致无限循环，但可以在条件语句中根据某些条件调用。

```javascript
class MyComponent extends React.Component {
  componentDidUpdate(prevProps, prevState) {
    if (prevProps.someValue !== this.props.someValue) {
      fetch('/api/data')
        .then(response => response.json())
        .then(data => this.setState({ data }));
    }
  }
}
```

## 三、卸载阶段（Unmounting）

卸载阶段是组件从 DOM 中移除的过程。这个阶段只有一个生命周期方法：

### 3.1 **componentWillUnmount()**

`componentWillUnmount` 方法在组件卸载并销毁之前调用。主要用于清理资源，如定时器、取消网络请求或清除订阅。

```javascript
class MyComponent extends React.Component {
  componentWillUnmount() {
    clearInterval(this.interval);
  }
}
```

## 四、错误处理阶段（Error Handling）

React 提供了一些特殊的生命周期方法，用于处理组件中的错误：

### 4.1 **static getDerivedStateFromError(error)**

`getDerivedStateFromError` 方法当渲染过程、生命周期方法或子组件抛出错误时调用。它返回一个对象来更新状态，以便下一次渲染能够显示降级 UI。

```javascript
class MyComponent extends React.Component {
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

### 4.2 **componentDidCatch(error, info)**

`componentDidCatch` 方法当渲染过程、生命周期方法或子组件抛出错误时调用。

错误边界（Error Boundary） 是 React 组件，并不是损坏的组件树。错误边界捕捉发生在子组件树中任意地方的 JavaScript 错误，打印错误日志，并且显示回退的用户界面。错误边界捕捉渲染期间、在生命周期方法中和在它们之下整棵树的构造函数中的错误。

错误边界可以捕获以下错误：

- 生命周期方法中的错误。
- 渲染过程中抛出的错误。
- 子组建树中的错误。
- 构造函数中的错误。

但是，错误边界无法捕获以下错误：

- 事件处理函数中的错误（需要使用 `try-catch` 手动捕获）。
- 异步代码中的错误（如 `setTimeout()` 或 Promise 等）。
- 错误边界自身抛出的错误。
- 服务端渲染中的错误。

```javascript
class MyComponent extends React.Component {
  componentDidCatch(error, info) {
    // 发送错误信息到远程服务器
    logErrorToMyService(error, info);
  }
}
```

## 五、使用 Hooks 管理生命周期

React Hooks 提供了一些函数来管理函数组件的生命周期：

- **useEffect**：类似于 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 的组合。
- **useLayoutEffect**：类似于 `componentDidMount` 和 `componentDidUpdate`，但它在所有 DOM 变更后同步调用。
- **useState**：用于在函数组件中管理 `state`。
- **useReducer**：用于在函数组件中管理复杂的 `state` 逻辑。
- **useContext**：用于在函数组件中访问上下文（Context）。

```javascript
import React, { useState, useEffect } from 'react';

function MyComponent() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 组件挂载和更新时执行
    document.title = `Count: ${count}`;
    return () => {
      // 组件卸载时执行
      document.title = 'Cleanup';
    };
  }, [count]); // 依赖数组

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

参考[react-lifecycle-methods-diagram](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)。
