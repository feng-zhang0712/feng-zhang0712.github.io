# React 的状态保存

## 一、HTTP 中的 `keep-alive`

## 二、React 中的状态保存

### 2.1 介绍

在 React 应用中实现状态自动保存（KeepAlive）功能，可以确保组件在卸载后重新挂载时能够恢复之前的状态。这种功能在许多场景中很有用，比如表单数据的自动保存、分页数据的恢复等。

### 2.2 实现方式

#### （1）使用 Context 与 useReducer

可以使用 React 的 Context 来保存状态，使得状态在组件卸载时不会丢失。

```javascript
// context.js
import React, { createContext, useReducer, useContext } from 'react';

const StateContext = createContext();

const initialState = {
  componentState: {}
};

function stateReducer(state, action) {
  switch (action.type) {
    case 'SAVE_STATE':
      return {
        ...state,
        componentState: {
          ...state.componentState,
          [action.component]: action.payload
        }
      };
    default:
      return state;
  }
}

export const StateProvider = ({ children }) => {
  const [state, dispatch] = useReducer(stateReducer, initialState);
  return (
    <StateContext.Provider value={{ state, dispatch }}>
      {children}
    </StateContext.Provider>
  );
};

export const useGlobalState = () => useContext(StateContext);

// MyComponent.js
import React, { useEffect, useState } from 'react';
import { useGlobalState } from './context';

function MyComponent() {
  const { state, dispatch } = useGlobalState();
  const [localState, setLocalState] = useState(state.componentState.MyComponent || {});

  useEffect(() => {
    return () => {
      dispatch({
        type: 'SAVE_STATE',
        component: 'MyComponent',
        payload: localState
      });
    };
  }, [localState, dispatch]);

  return (
    <div>
      <input
        type="text"
        value={localState.inputValue || ''}
        onChange={(e) => setLocalState({ ...localState, inputValue: e.target.value })}
      />
    </div>
  );
}

export default MyComponent;

// App.js
import React from 'react';
import { StateProvider } from './context';
import MyComponent from './MyComponent';

function App() {
  return (
    <StateProvider>
      <MyComponent />
    </StateProvider>
  );
}

export default App;
```

#### （2）使用浏览器的存储机制

可以利用浏览器的 `localStorage` 或 `sessionStorage` 来保存组件的状态。

```javascript
import React, { useState, useEffect } from 'react';

function KeepAliveComponent() {
  const [inputValue, setInputValue] = useState('');

  // 从 localStorage 中恢复状态
  useEffect(() => {
    const savedValue = localStorage.getItem('inputValue');
    if (savedValue) {
      setInputValue(savedValue);
    }
  }, []);

  // 保存状态到 localStorage
  useEffect(() => {
    localStorage.setItem('inputValue', inputValue);
  }, [inputValue]);

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
    </div>
  );
}

export default KeepAliveComponent;
```

在这个示例中，我们使用了 `useEffect` Hook 来在组件挂载时从 `localStorage` 恢复状态，并在状态更新时将其保存到 `localStorage` 中。

#### （3）使用自定义 Hook

可以创建一个自定义 Hook 来封装状态的自动保存逻辑，使其在多个组件中复用。

```javascript
import { useState, useEffect } from 'react';

function useKeepAliveState(key, initialValue) {
  const [state, setState] = useState(() => {
    const savedValue = localStorage.getItem(key);
    return savedValue !== null ? JSON.parse(savedValue) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);

  return [state, setState];
}

export default useKeepAliveState;
```

使用自定义 Hook 的组件。

```javascript
import React from 'react';
import useKeepAliveState from './useKeepAliveState';

function KeepAliveComponent() {
  const [inputValue, setInputValue] = useKeepAliveState('inputValue', '');

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
    </div>
  );
}

export default KeepAliveComponent;
```

#### （4）使用 Redux 或 Context

如果应用使用了全局状态管理库（如 Redux 或 Context），可以将组件的状态保存在全局状态中，确保状态在组件卸载和重新挂载时能够恢复。

创建 Redux 状态管理逻辑：

```javascript
// actions.js
export const setInputValue = (value) => ({
  type: 'SET_INPUT_VALUE',
  payload: value,
});

// reducer.js
const initialState = {
  inputValue: '',
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case 'SET_INPUT_VALUE':
      return {
        ...state,
        inputValue: action.payload,
      };
    default:
      return state;
  }
};

export default reducer;
```

配置 Redux store：

```javascript
// store.js
import { createStore } from 'redux';
import reducer from './reducer';

const store = createStore(reducer);

export default store;
```

使用 Redux Provider 包装应用：

```javascript
// index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

在组件中使用 Redux：

```javascript
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { setInputValue } from './actions';

function KeepAliveComponent() {
  const inputValue = useSelector((state) => state.inputValue);
  const dispatch = useDispatch();

  const handleChange = (e) => {
    dispatch(setInputValue(e.target.value));
  };

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={handleChange}
      />
    </div>
  );
}

export default KeepAliveComponent;
```

#### （5）使用第三方库（如 `react-keep-alive`）

可以使用第三方库来实现状态自动保存和恢复。比如 [`react-keep-alive`](https://github.com/StructureBuilder/react-keep-alive) 库可以帮助实现这一功能。

```javascript
import React from 'react';
import { KeepAlive, AliveScope } from 'react-keep-alive';

function KeepAliveComponent() {
  const [inputValue, setInputValue] = useState('');

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
      />
    </div>
  );
}

function App() {
  return (
    <AliveScope>
      <KeepAlive name="KeepAliveComponent">
        <KeepAliveComponent />
      </KeepAlive>
    </AliveScope>
  );
}

export default App;
```

在这个示例中，`react-keep-alive` 库确保 `KeepAliveComponent` 在卸载和重新挂载时能够保持其状态。
