`useReducer` 是 React 中用于管理复杂状态逻辑的 Hook。它提供了一种替代 `useState` 的方式，适用于状态变化逻辑较复杂的场景，如多步骤的表单、复杂的状态更新逻辑等。`useReducer` 通过将状态更新逻辑集中在一个 reducer 函数中，使得状态管理更加清晰和可预测。

### 基本用法

`useReducer` 的使用方式类似于 Redux，包含三个核心部分：当前状态、一个由 dispatch 触发的动作以及一个 reducer 函数。

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

- `state`：当前的状态值。
- `dispatch`：用于触发状态更新的函数。
- `reducer`：一个纯函数，接收当前状态和动作，返回新的状态。
- `initialState`：初始状态，可以是任意类型。

### 定义 Reducer 函数

Reducer 函数是一个纯函数，它接收当前状态和一个动作，并返回新的状态。它通常使用 `switch` 语句来处理不同的动作类型。

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}
```

### 示例：计数器

下面是一个使用 `useReducer` 实现简单计数器的示例：

```jsx
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}

export default Counter;
```

### 复杂状态逻辑

当状态逻辑较复杂时，`useReducer` 可以帮助你更好地组织和管理状态。例如，一个多步骤表单的状态管理可能涉及多个字段和多种状态变化。

```jsx
import React, { useReducer } from 'react';

const initialState = {
  step: 1,
  formData: {
    name: '',
    email: '',
    password: '',
  },
};

function reducer(state, action) {
  switch (action.type) {
    case 'nextStep':
      return { ...state, step: state.step + 1 };
    case 'prevStep':
      return { ...state, step: state.step - 1 };
    case 'updateField':
      return {
        ...state,
        formData: {
          ...state.formData,
          [action.field]: action.value,
        },
      };
    default:
      throw new Error();
  }
}

function MultiStepForm() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const handleChange = (e) => {
    dispatch({
      type: 'updateField',
      field: e.target.name,
      value: e.target.value,
    });
  };

  return (
    <div>
      {state.step === 1 && (
        <div>
          <h2>Step 1: Personal Info</h2>
          <input name="name" value={state.formData.name} onChange={handleChange} placeholder="Name" />
          <input name="email" value={state.formData.email} onChange={handleChange} placeholder="Email" />
          <button onClick={() => dispatch({ type: 'nextStep' })}>Next</button>
        </div>
      )}
      {state.step === 2 && (
        <div>
          <h2>Step 2: Password</h2>
          <input name="password" value={state.formData.password} onChange={handleChange} placeholder="Password" />
          <button onClick={() => dispatch({ type: 'prevStep' })}>Back</button>
          <button onClick={() => dispatch({ type: 'nextStep' })}>Next</button>
        </div>
      )}
      {state.step === 3 && (
        <div>
          <h2>Step 3: Review</h2>
          <p>Name: {state.formData.name}</p>
          <p>Email: {state.formData.email}</p>
          <p>Password: {state.formData.password}</p>
          <button onClick={() => dispatch({ type: 'prevStep' })}>Back</button>
          <button onClick={() => console.log('Form submitted', state.formData)}>Submit</button>
        </div>
      )}
    </div>
  );
}

export default MultiStepForm;
```

### 惰性初始化

`useReducer` 支持惰性初始化，这对于初始状态需要复杂计算的情况非常有用。你可以传递一个初始化函数，它接收初始参数并返回初始状态。

```jsx
function init(initialCount) {
  return { count: initialCount };
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter({ initialCount }) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
    </div>
  );
}

export default Counter;
```

### 总结

`useReducer` 是一个强大的 Hook，适用于具有复杂状态逻辑的组件。通过将状态更新逻辑集中在 reducer 函数中，它使得状态管理更加清晰和可预测。以下是一些使用 `useReducer` 的场景：

- 状态更新逻辑复杂且包含多个子值。
- 需要根据动作类型执行不同的状态更新。
- 想要将状态管理逻辑集中在一个地方。
