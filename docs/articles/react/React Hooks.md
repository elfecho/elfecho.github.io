React Hooks 是 React 16.8 引入的一项新特性，允许在函数组件中使用状态和其他 React 功能。自定义 Hooks 更是增强了组件的复用性和可读性。在这篇博客中，我们将介绍 React 中的基本 Hooks，如 `useState` 和 `useEffect`，以及如何创建自定义 Hooks，最后还将探讨一些常见的第三方 Hooks 使用示例。

## 1. useState

`useState` 是 React 中用于管理状态的基本 Hook。它允许你在函数组件中声明一个状态变量，并提供一个更新该状态的函数。

示例

```jsx
import React, { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0); // 初始化 count 为 0

  return (
    <div>
      <p>当前计数: {count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
      <button onClick={() => setCount(count - 1)}>减少</button>
    </div>
  );
};

export default Counter;
```

在这个例子中，我们使用 `useState` 创建了一个名为 `count` 的状态变量，初始值为 0。`setCount` 函数用于更新 `count` 的值。

### 1.1. 异步更新

React 的状态更新是异步的，这意味着更新可能不会立即反映在组件中。这尤其在处理多个状态更新时显得重要，因为多个更新可能会被合并，而不是立即执行。

示例：异步更新

```javascript
const Counter = () => {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1); // 第一次更新
    setCount(count + 1); // 第二次更新
    console.log(count); // 输出可能是更新前的状态
  };

  return (
    <div>
      <p>当前计数: {count}</p>
      <button onClick={handleClick}>增加</button>
    </div>
  );
};

```
### 1.2. 状态合并

当你用 `setCount` 更新状态时，React 会自动合并状态变化。这在使用对象状态时尤其重要。举个例子：
```javascript
const UserProfile = () => {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });

  const updateName = (newName) => {
    setUser({ ...user, name: newName }); // 合并更新
  };

  const updateAge = (newAge) => {
    setUser({ ...user, age: newAge }); // 合并更新
  };

  return (
    <div>
      <p>姓名: {user.name}</p>
      <p>年龄: {user.age}</p>
      <button onClick={() => updateName('Bob')}>更新姓名</button>
      <button onClick={() => updateAge(30)}>更新年龄</button>
    </div>
  );
};

```
### 1.3. 使用函数更新状态

为了防止状态更新的异步性导致的问题，React 提供了一种使用函数更新状态的方式。这种方式接收一个函数并将当前状态作为参数传递给它。这种方法确保了你总是使用最新的状态。

示例：使用函数更新状态

```javascript
const Counter = () => {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(prevCount => prevCount + 1); // 使用函数更新
    setCount(prevCount => prevCount + 1); // 通过函数确保更新
  };

  return (
    <div>
      <p>当前计数: {count}</p>
      <button onClick={handleClick}>增加</button>
    </div>
  );
};

```
在这个例子中，`setCount(prevCount => prevCount + 1)` 确保了我们在每次更新时都使用了最新的 `count` 值。无论有多少次状态更新，`prevCount` 始终是最新的，避免了由于异步更新导致的问题。

### 1.4 不可变数据

**不可变数据**（Immutable Data）是指一旦创建，其状态无法被改变的数据结构。任何对不可变数据的修改都会返回一个新的数据结构，而不会改变原始数据。这种特性在 React 中非常重要，尤其是在状态管理和性能优化方面。

不可变数据的特性

1. **保持原始数据不变**：使用不可变数据时，你不会直接修改原始对象或数组，而是创建一个新的对象或数组。
    
2. **便于追踪变化**：由于不可变数据的特性，React 可以更轻松地追踪数据的变化。它可以通过对比当前和前一个状态的引用（而不是逐个字段对比）来判断组件是否需要重新渲染。
    
3. **性能优化**：不可变数据可以利用引用比较 (shallow comparison) 来优化性能。在 React 中，组件的重新渲染可以基于 props 和 state 的引用进行判断，从而提高更新效率。

#### 1. 更新状态时保持不可变性

使用对象或数组时，确保每次更新都返回一个新的对象或数组，而不是修改原有的状态。

**对象状态更新示例**：

```javascript
const UserProfile = () => {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });

  const updateName = (newName) => {
    // 创建一个新的用户对象，保持原有状态不变
    // **不可变数据**- 不去修改 state 的值，而是要传入一个新的值 - 重要!
    setUser((prevUser) => ({ 
	    ...prevUser, // 解构语法
	    name: newName 
	}));
  };

  return (
    <div>
      <p>姓名: {user.name}</p>
      <p>年龄: {user.age}</p>
      <button onClick={() => updateName('Bob')}>更新姓名</button>
    </div>
  );
};

```
在这个示例中，使用了对象展开运算符 (`...prevUser`) 来创建一个新的用户对象。`setUser` 会更新状态为新对象，而不改变原有的 `user` 对象。

**数组状态更新示例**：

```javascript
const TodoList = () => {
  const [todos, setTodos] = useState(['学习 React', '学习 Redux']);

  const addTodo = (newTodo) => {
    // 创建一个新的数组，保持原有状态不变
    setTodos((prevTodos) => [...prevTodos, newTodo]);
    // 或者用以下方式
    setTodos((prevTodos) => prevTodos.concat(newTodo))
  };

  return (
    <div>
      <ul>
        {todos.map((todo, index) => (
          <li key={index}>{todo}</li>
        ))}
      </ul>
      <button onClick={() => addTodo('学习 TypeScript')}>添加待办事项</button>
    </div>
  );
};
```

在这个示例中，使用展开运算符 `...prevTodos` 创建一个新的待办事项数组。

#### 2. 不可变数据的库

为了更轻松地管理不可变数据，社区中有一些库可以帮助你处理复杂的数据结构：

- **Immutable.js**：一个用于创建不可变数据结构的库。它提供了 List、Map、Set 等数据结构，能够高效地处理大量数据和复杂操作。
    
- **Immer**：一个用于简化不可变数据处理的库。它允许你使用可变的方式来编写代码，同时在内部生成不可变的状态更新。这使得编写代码更加简洁。
    

**Immer 使用示例**：

```javascript
import { produce } from 'immer'

const UserProfile = () => {
  const [user, setUser] = useState({ name: 'Alice', age: 25 });

  const updateName = (newName) => {
    setUser(produce(user, draft => {
      draft.name = newName; // 直接修改草稿对象
    }));
  };

  return (
    <div>
      <p>姓名: {user.name}</p>
      <p>年龄: {user.age}</p>
      <button onClick={() => updateName('Bob')}>更新姓名</button>
    </div>
  );
};
```

在这个示例中，使用了 `Immer` 的 `produce` 函数，允许我们在可变的草稿对象上进行操作，同时返回一个新的不可变状态。

#### 总结

- **不可变数据** 是指数据一旦创建就无法更改，任何修改都会生成一个新的数据结构。
- 在 React 中使用不可变数据能够提高性能，便于追踪状态变化。
- 更新状态时，确保返回新的对象或数组，而不是直接修改原有状态。
- 可以使用专门的库（如 Immutable.js 或 Immer）来简化不可变数据的管理和操作。
## 2. useEffect

`useEffect` 是用于处理副作用的一种 Hook。副作用包括数据获取、订阅及手动操作 DOM 等。它可以看作是 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 的合并。

- 当组件渲染完成时，加载一个 Ajax 网络请求
- 当某个 state 更新时，加载一个 Ajax 网络请求
### 基本用法

`useEffect` 接受两个参数：

1. **callback 函数**：在组件渲染后执行的副作用。
2. **依赖数组**（可选）：决定副作用何时需要重新执行。

```jsx
import React, { useEffect } from 'react';

const ExampleComponent = () => {
  useEffect(() => {
    // 副作用逻辑，比如数据获取
    console.log('组件已挂载或更新');

    // 清理函数（可选）
    return () => {
      console.log('组件将卸载');
    };
  }, []); // 空数组表示只在组件挂载时执行一次

  return <div>我的组件</div>;
};

```

### 1. 组件创建（挂载）

当组件第一次渲染到 DOM 时，`useEffect` 中的 callback 函数会被执行。此时，我们可以执行一些初始化操作，比如数据获取。

```javascript
useEffect(() => {
  console.log('组件挂载');
  // 数据获取或订阅等

  // 清理函数 (可选)
  return () => {
    console.log('清理操作');
  };
}, []); // 依赖数组为空，表示仅在挂载时执行

```
### 2. 组件更新

当组件的状态或 props 更新时，如果依赖数组包含相关的值，`useEffect` 将再次运行。在这里，我们可以响应状态变化，比如重新获取数据或更新 DOM。

```javascript
const [count, setCount] = useState(0);

useEffect(() => {
  console.log('组件更新，当前 count:', count);
}, [count]); // 当 count 变化时执行副作用

```

### 3. 组件销毁（卸载）

当组件从 DOM 中移除时，`useEffect` 中的清理函数会被调用。这是进行清理操作的地方，比如取消订阅或清除定时器。

```javascript
useEffect(() => {
  const timer = setTimeout(() => {
    console.log('定时器触发');
  }, 1000);

  // 清理函数
  return () => {
    clearTimeout(timer);
    console.log('组件卸载，定时器被清除');
  };
}, []); // 依赖数组为空，表示仅在挂载时设置定时器

```

### 依赖数组的使用

依赖数组是 `useEffect` 的一个重要部分。它决定了 `useEffect` 何时执行：

- **空数组** (`[]`)：表示副作用仅在组件挂载时执行一次。
- **包含变量**：当数组中的变量发生变化时，副作用会重新执行。

```javascript
useEffect(() => {
  console.log('组件更新');
}, [props.someValue]); // 每当 props.someValue 变化时执行
```

### useEffect执行两次

- React 18开始，useEffect 在开发环境下会执行两次
- 模拟组件创建、销毁、再创建的完整流程，及早暴露问题
- 生产环境下会执行一次

## 3. 其他内置Hooks

#### 1. useRef

`useRef` 用于获取对 DOM 元素的引用或保存可变的值，这些值不会引起组件的重新渲染。它在处理焦点、文本选择或媒体播放等场景时特别有用。

```typescript
import React, { FC, useRef } from 'react'
const UseRefDemo: FC = () => {
  const inputRef = useRef<HTMLInputElement>(null)
  function selectInput() {
    const inputElem = inputRef.current
    if (inputElem) inputElem.select()
  }
  const nameRef = useRef('张三')
  function changeName() {
    nameRef.current = '李四' //修改 ref 值，不会触发 rerender
  }
  return (
    <>
      <div>
        <input type="text" ref={inputRef} />
        <button onClick={selectInput}>选中 input</button>
      </div>
      <div>
        <p>name: {nameRef.current}</p>
        <button onClick={changeName}>改变 name</button>
      </div>
    </>
  )
} 
export default UseRefDemo
```

##### 使用 `useRef` 解决闭包问题

在 React 中，闭包问题是一个常见的陷阱，尤其是在处理异步操作和事件处理时。由于 JavaScript 的函数作用域和 React 组件的生命周期之间的关系，使用状态或属性值可能会导致意外的行为。本文将介绍如何使用 `useRef` Hook 来解决这些闭包问题。

##### 什么是闭包问题？

闭包问题通常发生在你使用一个外部变量时，这个变量在函数被定义时已经被捕获。在 React 中，当组件重新渲染时，状态和属性的值会改变，而之前定义的函数仍然会引用旧的值。这可能导致逻辑错误，特别是在处理定时器、事件监听器或异步请求时。

示例：闭包问题的典型场景

考虑下面的示例，它展示了一个计数器，当你点击按钮时，它会在 1 秒后输出当前的计数值：

```typescript
import React, { useState } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setTimeout(() => {
      console.log(`当前计数: ${count}`); // 闭包问题
    }, 1000);
  };

  return (
    <div>
      <p>计数: {count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
      <button onClick={handleClick}>输出计数</button>
    </div>
  );
};

export default Counter;

```

在这个例子中，不管你点击"增加"按钮多少次，`handleClick` 中的 `count` 始终会输出初始值 0，因为 `setTimeout` 内的函数捕获了 `count` 的旧值。

##### 使用 `useRef` 解决闭包问题

`useRef` 是一个用于访问 DOM 元素或保存任何可变值的 Hook，且这些值在组件的生命周期内不会引起重新渲染。我们可以利用 `useRef` 来存储最新的状态值，从而解决闭包问题。

修改示例以使用 `useRef`

我们可以通过 `useRef` 来存储最新的 `count` 值，并在 `setTimeout` 中使用这个值。修改后的代码如下：

```typescript
import React, { useState, useEffect, useRef } from 'react';

const Counter = () => {
  const [count, setCount] = useState(0);
  const countRef = useRef(count); // 使用 useRef 来保存最新的 count

  // 使用 useEffect 来更新 countRef 的值
  useEffect(() => {
    countRef.current = count;
  }, [count]);

  const handleClick = () => {
    setTimeout(() => {
      console.log(`当前计数: ${countRef.current}`); // 使用 countRef.current
    }, 1000);
  };

  return (
    <div>
      <p>计数: {count}</p>
      <button onClick={() => setCount(count + 1)}>增加</button>
      <button onClick={handleClick}>输出计数</button>
    </div>
  );
};

export default Counter;
```

在这个修改后的示例中：

1. **使用 `useRef` 创建 `countRef`**：`countRef` 用于存储最新的 `count` 值。`useRef` 返回一个可变的 `current` 属性，我们可以自由地更新它而不会导致组件重新渲染。
    
2. **使用 `useEffect` 更新 `countRef`**：每当 `count` 更新时，通过 `useEffect` 更新 `countRef.current` 的值，以确保它始终保持最新。
    
3. **在 `setTimeout` 中使用 `countRef.current`**：在 `setTimeout` 的回调函数中，我们使用 `countRef.current` 而不是 `count`，这样就能确保我们获取的是最新的计数值。
### 2. useMemo

`useMemo` 接受两个参数：

1. **计算函数**：返回要记忆的值的函数。
2. **依赖数组**：一个数组，只有在数组中的依赖项发生变化时，计算函数才会被重新执行。

示例

```typescript
import React, { useMemo, useState } from 'react'
const UseMemoDemo = () => {
  console.log('demo...')
  const [num1, setNum1] = useState(10)
  const [num2, setNum2] = useState(10)
  const [text, setText] = useState('hello')
  const sum = useMemo(() => {
    console.log('sum changed')
    return num1 + num2 // 缓存
  }, [num1, num2])
  return (
    <div>
      <p>{sum}</p>
      <p>
        {num1} <button onClick={() => setNum1(num1 + 1)}>add num1</button>
      </p>
      <p>
        {num2} <button onClick={() => setNum2(num2 + 1)}>add num2</button>
      </p>
      <div>
        <input
          type="text"
          onChange={e => setText(e.target.value)}
          value={text}
        />
      </div>
    </div>
  )
}
export default UseMemoDemo
```

### 3. useCallback
`useCallback` 是 React 提供的一个 Hook，用于缓存函数实例以优化性能。它可以防止在每次组件重新渲染时创建新的函数实例，从而减少子组件不必要的重新渲染。

#### `useCallback` 的基本用法

`useCallback` 接受两个参数：

1. **回调函数**：需要缓存的函数。
2. **依赖数组**：只有在数组中的依赖项发生变化时，回调函数才会被重新创建。

示例

```typescript
import React, { useCallback, useState } from 'react';

const Button = React.memo(({ onClick, children }) => {
  console.log('按钮组件重新渲染');
  return <button onClick={onClick}>{children}</button>;
});

const App = () => {
  const [count, setCount] = useState(0);
  
  // 使用 useCallback 缓存增加计数的函数
  const increment = useCallback(() => {
    setCount(c => c + 1);
  }, []); // 依赖项为空数组，意味着该函数只会在组件挂载时创建一次

  return (
    <div>
      <p>计数: {count}</p>
      <Button onClick={increment}>增加</Button>
    </div>
  );
};

export default App;

```

在上面的示例中：

- `Button` 组件被包装在 `React.memo` 中，以防止不必要的重新渲染。
- `increment` 函数使用 `useCallback` 进行缓存，这样在 `App` 组件每次渲染时，`increment` 函数的引用都是相同的。
- 因为 `increment` 函数没有依赖任何外部状态，所以它只在组件挂载时创建一次。

#### 何时使用 `useCallback`

使用 `useCallback` 主要有以下几个场景：

1. **子组件的性能优化**：如果你有一个子组件依赖于父组件传递的函数（如上面的 `Button`），并且这个子组件被 `React.memo` 包装，使用 `useCallback` 可以防止子组件因函数引用的变化而不必要地重新渲染。
    
2. **依赖性管理**：如果你的函数依赖于组件的状态或属性，使用 `useCallback` 可以确保只有在相关依赖变化时才重新创建函数。
    
3. **避免闭包问题**：在某些情况下，直接定义的函数可能会捕获某些值的旧状态，使用 `useCallback` 可以帮助你控制函数的依赖，从而避免闭包问题。

#### 注意事项

- **不滥用**：过度使用 `useCallback` 可能导致代码变得更加复杂，只有在性能问题显著时才使用。
- **性能损耗**：为每个函数使用 `useCallback` 可能导致性能损耗，因为 React 仍然需要跟踪这些函数的依赖。如果缓存的函数非常简单，使用 `useCallback` 可能会比创建新的函数更慢。

#### 总结

`useCallback` 是一个非常有用的工具，用于缓存函数以优化 React 应用的性能。它可以帮助开发者避免不必要的重新渲染，尤其是在与子组件结合使用时。

在使用时，建议只在确实需要优化性能的情况下使用 `useCallback`，并保持代码的简洁性和可读性。
## 4. 自定义 Hooks

自定义 Hooks 是将逻辑提取到可复用的函数中。它们可以使用内置 Hooks 和其他自定义 Hooks。

### 示例

```jsx
import { useState, useEffect } from 'react';

// 自定义 Hook
const useFetch = (url) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch(url);
      const result = await response.json();
      setData(result);
      setLoading(false);
    };

    fetchData();
  }, [url]); // 当 URL 改变时重新获取数据

  return { data, loading };
};

// 使用自定义 Hook 的组件
const UserFetcher = () => {
  const { data, loading } = useFetch('https://api.example.com/users');

  if (loading) {
    return <p>加载中...</p>;
  }

  return (
    <div>
      <h1>用户列表:</h1>
      <ul>
        {data.map(user => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
};

export default UserFetcher;
```

在这个例子中，我们创建了一个名为 `useFetch` 的自定义 Hook，用于获取数据。随后在 `UserFetcher` 组件中使用这个 Hook。

## 5. 常见第三方 Hooks

除了内置的 Hooks，许多社区成员创建了有用的第三方 Hooks。以下是一些常见的第三方 Hooks 及其用法。

### 5.1 React Router Hooks

`react-router-dom` 提供了一些 Hooks 用于处理路由，例如 `useHistory`、`useLocation` 和 `useParams`。

**使用示例**：

```jsx
import React from 'react';
import { useHistory } from 'react-router-dom';

const HomeButton = () => {
  const history = useHistory();

  return (
    <button onClick={() => history.push('/')}>
      返回首页
    </button>
  );
};

export default HomeButton;
```

### 5.2 React Query

`react-query` 是一个用于数据获取和缓存的库。它提供了 `useQuery` 和 `useMutation` 等 Hooks。

**使用示例**：

```jsx
import React from 'react';
import { useQuery } from 'react-query';

const fetchUsers = async () => {
  const response = await fetch('https://api.example.com/users');
  return response.json();
};

const UsersList = () => {
  const { data, error, isLoading } = useQuery('users', fetchUsers);

  if (isLoading) return <p>加载中...</p>;
  if (error) return <p>出错了: {error.message}</p>;

  return (
    <ul>
      {data.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

export default UsersList;
```

### 5.3 useMediaQuery

`use-media-query` 是一个用于处理媒体查询的自定义 Hook，适用于响应式设计。

**使用示例**：

```jsx
import React from 'react';
import useMediaQuery from 'use-media-query-hook';

const ResponsiveComponent = () => {
  const isMobile = useMediaQuery('(max-width: 768px)');

  return (
    <div>
      {isMobile ? (
        <p>这是移动设备视图。</p>
      ) : (
        <p>这是桌面设备视图。</p>
      )}
    </div>
  );
};

export default ResponsiveComponent;
```

## Hooks 使用规则

- 必须用 useXxx 格式来命名
- 只能在两个地方调用 Hook(组件内，其他 Hook 内)
- 必须保证每次的调用顺序一致(不能放在 if for 内部)

## 总结

React Hooks 的引入使得在函数组件中管理状态和副作用变得更加便捷。通过使用 `useState` 和 `useEffect`，你可以轻松地管理组件的状态和副作用。而自定义 Hooks 则为逻辑的复用提供了强大的支持。此外，许多第三方库提供的 Hooks 进一步丰富了我们的开发工具，使得状态管理和数据获取更加高效。
