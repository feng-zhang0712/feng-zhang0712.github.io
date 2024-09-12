在React中，Portal是一种将子组件渲染到DOM树中的不同位置的技术，而不是其父组件的DOM层次结构中。Portal的主要用途是处理那些需要在DOM树特定位置渲染的组件，例如模态框、弹出菜单和通知等，从而确保这些组件能够正确处理CSS样式和事件。

### 基本用法

`ReactDOM.createPortal` 方法用于创建Portal，它接受两个参数：要渲染的子元素和目标DOM节点。

```javascript
ReactDOM.createPortal(child, container)
```

- `child`: 要渲染的React子元素。
- `container`: 目标DOM节点，子元素将被渲染到此节点中。

#### 示例

下面是一个简单的示例，展示如何使用Portal将一个模态框渲染到`document.body`中，而不是其父组件的DOM层次结构中。

```javascript
// basic-usage.js

import React from 'react';
import ReactDOM from 'react-dom';

class Modal extends React.Component {
  render() {
    return ReactDOM.createPortal(
      <div className='modal'>
        <div className='modal-content'>
          <span className='close' onClick={this.props.onClose}>&times;</span>
          <p>{this.props.children}</p>
        </div>
      </div>,
      document.body
    );
  }
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      showModal: false,
    };
  }

  toggleModal = () => {
    this.setState(prevState => ({
      showModal: prevState.showModal,
    }));
  }

  render() {
    return (
      <div>
        <button onClick={this.toggleModal}>Toggle Modal</button>
        {this.state.showModal && (
          <Modal onClose={this.toggleModal}>
            This is a modal rendered using a Portal.
          </Modal>
        )}
      </div>
    );
  }
}

export default App;
```

在这个示例中，`Modal` 组件使用 `ReactDOM.createPortal` 将其内容渲染到 `document.body`，而不是 `App` 组件的DOM层次结构中。这确保了模态框可以正确处理CSS样式和事件。

### Portal的优点

1. **事件冒泡**：虽然Portal将子元素渲染到不同的DOM节点中，但这些元素的事件冒泡行为仍然是按照React组件树的逻辑进行的。这意味着事件处理程序在父组件中仍然可以正常工作。

2. **样式问题的解决**：通过Portal渲染的元素可以避免父组件的CSS样式影响，从而确保样式的一致性。例如，模态框和弹出菜单通常需要独立于父组件的样式。

3. **DOM层次结构**：Portal允许开发者在不改变React组件树的情况下，将子元素渲染到DOM树中的不同位置，简化了组件的组织和管理。

### 事件冒泡示例

为了展示Portal的事件冒泡行为，可以看以下示例：

```javascript
// bubbling.js

import React from 'react';
import ReactDOM from 'react-dom';

class Modal extends React.Component {
  render() {
    return ReactDOM.createPortal(
      <div className="modal" onClick={this.props.onClose}>
        <div className="modal-content" onClick={e => e.stopPropagation()}>
          {this.props.children}
        </div>
      </div>,
      document.body
    );
  }
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      showModal: false,
    };
  }

  toggleModal = () => {
    this.setState(prevState => ({
      showModal: !prevState.showModal,
    }));
  }

  render() {
    return (
      <div onClick={() => console.log('App clicked')}>
        <button onClick={this.toggleModal}>Toggle Modal</button>
        {this.state.showModal && (
          <Modal onClose={this.toggleModal}>
            <p>This is a modal rendered using a Portal.</p>
          </Modal>
        )}
      </div>
    );
  }
}

export default App;
```

在这个示例中，点击模态框的背景区域会触发 `Modal` 的 `onClose` 方法，同时事件冒泡会触发 `App` 组件的 `onClick` 事件，打印 `App clicked`。但点击模态框内容区域时，由于 `stopPropagation` 方法的调用，事件不会冒泡到 `App` 组件。

### 结合 Context API 使用

在实际应用中，Portal 组件可能需要访问 React Context。以下是一个结合 Context API 使用 Portal 的示例：

```javascript
// with-context-api.js

import React from 'react';
import ReactDOM from 'react-dom';

const ThemeContext = React.createContext('light');

class Modal extends React.Component {
  static themeContext = ThemeContext;
  render() {
    const theme = themeContext.value;
    return (
      ReactDOM.createPortal(
        <div className={`modal ${theme}`} onClick={this.props.onClose}>
          <div className="modal-content" onClick={e => e.stopPropagation()}>
            {this.props.children}
          </div>
        </div>,
        document.body
      )
    )
  }
}

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      showModal: false,
    };
  }

  toggleModal = () => {
    this.setState(prevState => ({
      showModal: !prevState.showModal,
    }));
  }

  render() {
    return (
      <ThemeContext.Provider value='dark'>
        <div onClick={() => console.log('App clicked')}>
          <button onClick={this.toggleModal}>Toggle Modal</button>
          {this.state.showModal && (
            <Modal onClose={this.toggleModal}>
              <p>This is a modal rendered using a Portal with context.</p>
            </Modal>
          )}
        </div>
      </ThemeContext.Provider>
    );
  }
}

export default App;
```

在这个示例中，`Modal` 组件通过 `contextType` 属性访问 `ThemeContext`，并根据上下文值应用不同的样式。

### 总结

React的Portal是一个强大的工具，允许你将子组件渲染到DOM树中的不同位置，而不会影响React组件树的逻辑结构。它在处理模态框、弹出菜单和通知等场景时非常有用，可以解决样式冲突和事件处理问题。如果你在项目中遇到类似需求，Portal是一个值得考虑的解决方案。
