在 React 中，表单控件的管理可以分为受控组件（Controlled Components）和非受控组件（Uncontrolled Components）。它们代表了两种不同的状态管理方式，对于表单输入的处理有着各自的特点和使用场景。

### 受控组件（Controlled Components）

受控组件是指其输入值完全由 React 的 state 来管理的组件。每当输入值变化时，组件的 state 也会相应地更新，这样可以确保组件的值始终与 state 保持同步。

#### 特点

1. **一致性**：组件的值完全由 state 控制，保证了输入值和 state 之间的一致性。
2. **即时反馈**：通过事件处理函数（如 `onChange`），可以在输入值变化时即时获取和处理输入值。
3. **简单调试**：由于所有状态都在组件的 state 中管理，调试和追踪状态变化更加简单。

#### 示例

以下是一个受控组件的示例：

```javascript
import React, { useState } from 'react';

function ControlledInput() {
  const [inputValue, setInputValue] = useState('');

  const handleChange = (event) => {
    setInputValue(event.target.value);
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Submitted value:', inputValue);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Controlled Input:
        <input type="text" value={inputValue} onChange={handleChange} />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default ControlledInput;
```

在这个示例中，输入框的值完全由 `inputValue` 的 state 管理。每当输入变化时，`handleChange` 函数会更新 state，从而导致组件重新渲染。

### 非受控组件（Uncontrolled Components）

非受控组件是指其输入值由 DOM 自身来管理，而不是通过 React 的 state。通常使用 `ref` 来访问和操作 DOM 元素。

#### 特点

1. **简单实现**：不需要通过 state 管理输入值，代码实现较为简单。
2. **性能优化**：在某些情况下，可以减少 React 的重新渲染，提高性能。
3. **适用场景**：适用于需要直接访问 DOM 元素或集成第三方库的场景。

#### 示例

以下是一个非受控组件的示例：

```javascript
import React, { useRef } from 'react';

function UncontrolledInput() {
  const inputRef = useRef(null);

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Submitted value:', inputRef.current.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Uncontrolled Input:
        <input type="text" ref={inputRef} />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default UncontrolledInput;
```

在这个示例中，输入框的值由 DOM 自身管理。通过 `ref` 可以在表单提交时访问输入值。

### 选择受控组件与非受控组件

选择使用受控组件还是非受控组件，主要取决于具体的需求和场景：

- **受控组件**：
  - 需要即时反馈和验证输入值。
  - 需要严格控制输入值和 state 之间的一致性。
  - 需要在输入值变化时触发复杂逻辑。
  
- **非受控组件**：
  - 需要快速实现简单表单。
  - 输入值变化不需要即时反馈。
  - 需要集成第三方库或操作原生 DOM 元素。

### 受控组件与非受控组件对比

| 特点                | 受控组件 (Controlled Components) | 非受控组件 (Uncontrolled Components) |
|---------------------|---------------------------------|-------------------------------------|
| 状态管理             | 通过 React state 管理              | 通过 DOM 自身管理                        |
| 输入值访问           | 通过 state 访问                     | 通过 `ref` 访问                          |
| 代码复杂度           | 较高                              | 较低                                    |
| 即时反馈             | 支持                              | 不支持                                  |
| 性能                 | 可能较低                           | 可能较高                                |
| 适用场景             | 需要严格控制输入值、即时反馈、复杂逻辑 | 快速实现简单表单、集成第三方库           |

参考文章：

- [What are Controlled and Uncontrolled Components in React.js?](https://www.freecodecamp.org/news/what-are-controlled-and-uncontrolled-components-in-react)