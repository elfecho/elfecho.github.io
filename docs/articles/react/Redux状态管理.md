## 什么是 Context？

Context 是 React 提供的一种用于在组件树中共享数据的机制。它可以让我们在组件树中直接访问某些数据，而无需通过每一级的 props 进行传递。这在以下情况下特别有用：

- 数据需要在多个组件之间共享。
- 组件层次较深，导致 props 逐层传递变得繁琐。
- 希望避免“props drilling”问题。

## 创建 Context

使用 Context API 的第一步是创建一个 Context 对象。我们可以使用 `React.createContext` 方法来完成这一步：

javascript复制

```
import React from 'react';  const MyContext = React.createContext();
```

## 提供 Context 数据

一旦我们创建了 Context 对象，就可以使用它的 `Provider` 组件来提供数据。`Provider` 组件接受一个 `value` 属性，这个属性就是我们希望在整个组件树中共享的数据。

下面是一个简单的示例，我们将创建一个主题上下文，允许组件在树中获取当前主题：

javascript复制

`import React, { useState } from 'react';  // 创建 Context const ThemeContext = React.createContext();  const ThemeProvider = ({ children }) => {     const [theme, setTheme] = useState('light');      const toggleTheme = () => {         setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));     };      return (         <ThemeContext.Provider value={{ theme, toggleTheme }}>             {children}         </ThemeContext.Provider>     ); };`

在上述代码中，我们创建了一个 `ThemeProvider` 组件，使用 `useState` 来管理主题状态，并提供了一个切换主题的函数。

## 消费 Context 数据

要在组件中消费 Context 数据，我们可以使用 `Context.Consumer` 组件，或者在函数组件中使用 `useContext` 钩子。`useContext` 是推荐的方式，语法更简洁，使用起来也更加方便。

### 使用 `useContext`

下面是一个简单的示例，展示了如何在子组件中使用 `useContext` 来获取主题信息：

javascript复制

`import React, { useContext } from 'react';  const ThemedComponent = () => {     const { theme, toggleTheme } = useContext(ThemeContext);      return (         <div style={{ background: theme === 'light' ? '#fff' : '#333', color: theme === 'light' ? '#000' : '#fff' }}>             <h1>当前主题: {theme}</h1>             <button onClick={toggleTheme}>切换主题</button>         </div>     ); };`

### 将 Provider 包裹在应用中

最后，我们需要在应用的顶层组件中使用 `ThemeProvider` 来包裹所有子组件，以便让它们都可以访问到主题上下文：

javascript复制

`import React from 'react'; import ReactDOM from 'react-dom'; import { ThemeProvider } from './ThemeContext'; // 假设我们在 ThemeContext.js 中定义了 ThemeProvider import ThemedComponent from './ThemedComponent';  const App = () => (     <ThemeProvider>         <ThemedComponent />     </ThemeProvider> );  ReactDOM.render(<App />, document.getElementById('root'));`