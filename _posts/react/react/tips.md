## 在 React 中，如何判断是 class component，还是 function component ？

在使用 React 时，有时候你需要判断一个组件是类组件（Class Component）还是函数组件（Function Component）。以下是一些方法可以帮助你做出这个判断：

### 方法 1: `typeof` 运算符

对于函数组件，你可以使用 `typeof` 运算符判断它是否为函数。

```javascript
import React from 'react';

function isFunctionComponent(Component) {
  return (
    typeof Component === 'function' && // 是函数
    !(Component.prototype && Component.prototype.isReactComponent) // 不是类组件
  );
}
```

### 方法 2: `Component.prototype.isReactComponent`

类组件会有一个特定的原型属性 `isReactComponent`。你可以利用这个属性来判断组件是否为类组件。

```javascript
function isClassComponent(Component) {
  return (
    typeof Component === 'function' && // 是函数
    Component.prototype && Component.prototype.isReactComponent // 有 isReactComponent 属性
  );
}
```

### 方法 3: 使用 React 内置方法

React 提供了一些内置的方法和属性，可以帮助你区分类组件和函数组件。

#### 3.1 React.Component 和 React.PureComponent

```javascript
function isReactComponent(Component) {
  return Component.prototype instanceof React.Component || Component.prototype instanceof React.PureComponent;
}
```

#### 3.2 使用 `React.isValidElement`

你可以尝试使用 `React.isValidElement` 判断生成的元素是否有效，然后利用上述方法判断组件类型。

```javascript
import React from 'react';

function isFunctionComponent(Component) {
  return (
    typeof Component === 'function' &&
    !(Component.prototype && Component.prototype.isReactComponent)
  );
}

function isClassComponent(Component) {
  return (
    typeof Component === 'function' &&
    Component.prototype &&
    Component.prototype.isReactComponent
  );
}
```

### 综合示例

以下是一个综合示例，展示了如何同时判断一个组件是类组件还是函数组件：

```javascript
import React from 'react';

function isClassComponent(Component) {
  return (
    typeof Component === 'function' &&
    Component.prototype &&
    Component.prototype.isReactComponent
  );
}

function isFunctionComponent(Component) {
  return (
    typeof Component === 'function' &&
    !(Component.prototype && Component.prototype.isReactComponent)
  );
}

function isReactComponent(Component) {
  return isClassComponent(Component) || isFunctionComponent(Component);
}

// 测试
class ClassComponent extends React.Component {
  render() {
    return <div>Class Component</div>;
  }
}

function FunctionComponent() {
  return <div>Function Component</div>;
}

console.log(isClassComponent(ClassComponent)); // true
console.log(isFunctionComponent(ClassComponent)); // false
console.log(isClassComponent(FunctionComponent)); // false
console.log(isFunctionComponent(FunctionComponent)); // true
console.log(isReactComponent(ClassComponent)); // true
console.log(isReactComponent(FunctionComponent)); // true
```

在以上代码中，我们首先定义了判断是否为类组件和函数组件的函数 `isClassComponent` 和 `isFunctionComponent`，然后通过这些函数判断不同组件的类型。
