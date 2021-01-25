## React Hooks

之前笔记丢失，复习react-hooks并且补档。根据官方文档补充hooks使用方式。

### Hook的规则

Hook本质就是`JavaScript`函数，但是在使用它时需要遵循两条规则。

#### 只在最顶层使用 Hook

+ 不要在循环，条件或嵌套函数中调用 Hook，总是在你的 React 函数的最顶层调用他们。确保 Hook 在每一次渲染中都按照同样的顺序被调用。

#### 只在 React 函数中调用 Hook

 + 在React的函数组件中调用 Hook
 + 在自定义Hook中调用其他 Hook

### useState

```javascript
const [state, setState] = useState(initialState);
```

+ 创建一个状态提供访问或使用。
+ 返回一个数组，数组的第一项是状态，第二项是状态的修改函数。

`UseStateTest.js`

```javascript
import { useState } from "react";
function UseStateTest() {
  // 
  const [count, setCount] = useState(0);
  const handleIncreament = () => {
    setCount((count) => count + 1);
  };
  return (
    <div className="useState-test">
      <h2>useState</h2>
      <p>count:{count}</p>
      <button onClick={handleIncreament}>add</button>
      <hr/>
    </div>
  );
}

export default UseStateTest;

```

### useEffect

```javascript
useEffect(fn,dep);
```

+ 接受一个副作用处理函数，处理副作用。副作用处理函数会在组件渲染到屏幕之后执行。
+ 副作用处理函数可以返回一个副作用清理函数，用于清理副作用。会在卸载/重新挂载时执行。
+ `useEffect`函数的执行条件取决于依赖数组dep。

关于副作用，例如DOM操作，网络请求

`UseEffectTest.js`

```javascript
import { useEffect, useState } from "react";

function UseEffectTest() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch("http://jsonplaceholder.typicode.com/users")
      .then((res) => res.json())
      .then((data) => {
        setUsers(data);
      });
  }, []);
  return (
    <div>
      <h2>useEffect</h2>
      <ul>
        {users.map((user) => {
          return (
            <li key={user.id}>
              <p>{user.name}</p>
              <p>Email:{user.email}</p>
              <p>Phone:{user.phone}</p>
            </li>
          );
        })}
      </ul>
      <hr />
    </div>
  );
}

export default UseEffectTest;
```

### useReducer

```javascript
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

+ `useState`的替代方案。轻量的redux方案。
+ 在某些场景下，`useReducer` 会比 `useState` 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等。

`UseReducerTest.js`

```javascript
import { useReducer } from "react";

function UseReducerTest() {
  const reducer = (state, action) => {
    switch (action.type) {
      case "add":
        return { count: state.count + 1 };
      case "sub":
        return { count: state.count - 1 };
      case "reset":
        return { count: 0 };
      default:
        return state;
    }
  };
  const initialState = {
    count: 0,
  };
  const [state, dispatch] = useReducer(reducer, initialState);

  // 点击添加按钮
  const handleIncrement = () => {
    dispatch({
      type: "add",
    });
  };
  const handleDecrement = () => {
    dispatch({
      type: "sub",
    });
  };
  const handleReset = () => {
    dispatch({
      type: "reset",
    });
  };
  return (
    <div>
      <h2>UseReducerTest</h2>
      <div>
        <p>you have a count: {state.count}</p>
        <p>
          <button onClick={handleIncrement}>add</button>
          <button onClick={handleDecrement}>sub</button>
          <button onClick={handleReset}>reset</button>
        </p>
      </div>
    </div>
  );
}

export default UseReducerTest;

```

###  useCallback

```javascript
const memoizedCallback = useCallback(fn, dep);
```

+ 缓存一个函数，固化函数。返回被固化的函数。
+ 依赖数组决定useCallback是否执行。

`UseCallbackTest.js`

```javascript

```

### useMemo

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

+ 缓存值

`UseMemoTest.js`

```javascript
import { useMemo, useState } from "react";
function UseMemoTest(params) {
  const x = 20;
  const [y, setY] = useState(0);
  const memoizedPosition = useMemo(() => {
    return x + y;
  }, [y]);
  const handleIncrement = () => {
    setY((y) => y + 5);
  };
  return (
    <div className="usememo-test">
      <h2>UseMemo</h2>
      <p>{memoizedPosition}</p>
      <p>
        <button onClick={handleIncrement}>increment</button>
      </p>
      <hr />
    </div>
  );
}

export default UseMemoTest;
```

### useRef

```javascript
const refContainer = useRef(initialValue);
```

+ 一种访问DOM的主要方式。

`UseRefTest.js`

```javascript
import { useEffect, useRef } from "react";

function UseRefTest() {
  const inputRef = useRef();
  useEffect(() => {
    inputRef.current.focus();
  }, []);
  return (
    <div className="">
        <h2>useRef</h2>
      <input type="text" ref={inputRef} />
      <hr />
    </div>
  );
}

export default UseRefTest;
```

### useLayoutEffect

```javascript
useLayoutEffect(fn,dep);
```

+ 其函数签名与 `useEffect` 相同，但它会在所有的 DOM 变更之后<b>同步</b>调用 effect。

### 自定义hook

遵循hook的原则，创建自定义hook：

+ Hook本质就是`JavaScript`函数，但是在使用它时需要遵循两条规则。
+ 不要在循环，条件或嵌套函数中调用 Hook，总是在你的 React 函数的最顶层调用他们。
+ 在自定义Hook中调用其他 Hook
+ 在React的函数组件中调用 Hook

我们可以将请求封装在一个自定义hook中，例如：

`useGetTodos.js`

```javascript
import { useEffect, useState } from "react";
function useGetTodos() {
  const [todos, setUsers] = useState([]);
  useEffect(() => {
    fetch("http://jsonplaceholder.typicode.com/todos?_limit=10")
      .then((res) => res.json())
      .then((data) => {
        console.log(data);
        setUsers(data);
      });
  }, []);
  return todos;
}
export default useGetTodos;
```

然后在React组件中直接调用我们的hook。即将副作用抽离到其他函数。减轻了组件的负担，提高复用性，假设有其他位置用到相同的行为，直接调用封装好的hook即可。

`useCustomHookTest.js`

```javascript
import useGetTodos from "../../hook/useGetTodos";
function UseCustomHookTest() {
  const todos = useGetTodos();
  return (
    <div>
      <h2>CustomHook</h2>
      <ul>
        {todos.map((todo) => {
          return <li key={todo.id}>{todo.title}</li>;
        })}
      </ul>
      <hr />
    </div>
  );
}

export default UseCustomHookTest;
```



