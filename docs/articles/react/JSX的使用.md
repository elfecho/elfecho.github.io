
在现代前端开发中，React 是一个非常流行的库，它通过组件化的方式使得构建用户界面变得更加高效和灵活。而 JSX 是 React 中一种独特的语法扩展，它允许我们在 JavaScript 代码中书写 HTML 结构。本文将深入探讨 JSX 的语法、标签、属性以及如何监听 DOM 事件。

## 什么是 JSX？

JSX（JavaScript XML）是一种语法扩展，允许我们在 JavaScript 代码中嵌入 HTML。JSX 的主要目的是提高代码的可读性和可维护性。

### JSX 的基本语法

JSX 看起来很像 HTML，但它的行为有一些不同。以下是一个简单的 JSX 示例：

```jsx
const element = <h1>Hello, world!</h1>;
```

在这个例子中，`<h1>` 标签是一个 JSX 元素，它表示一个包含文本 "Hello, world!" 的标题。

### 注意事项

1. **标签必须闭合**：在 JSX 中，所有标签都必须闭合。如果是自闭合标签，例如 `<img>`，则需要写成 `<img />`。
2. **只有一个根元素**：一个 JSX 表达式必须有且只有一个根元素。

## JSX 标签

在 JSX 中，我们可以使用任何有效的 HTML 标签，也可以使用自定义的组件。下面是一些常见的标签：

### 1. HTML 标签

```jsx
const element = (
  <div>
    <h1>标题</h1>
    <p>这是一个段落。</p>
  </div>
);
```

### 2. 自定义组件

自定义组件的名称必须以大写字母开头：

```jsx
function MyComponent() {
  return <h1>这是我的组件!</h1>;
}

const element = <MyComponent />;
```

### 3. 嵌套标签

JSX 允许标签嵌套：`标签必须闭合`

```jsx
const element = (
  <div>
    <h1>这是一个标题</h1>
    <div>
      <p>这是一个段落。</p>
    </div>
  </div>
);
// 也可以使用 Fragment <></>
```

## 属性

在 JSX 中，我们可以为元素添加属性。属性的书写规则与 HTML 类似，但有一些特别之处。

### 1. 属性的写法

```jsx
const element = <>
	<img src="image.png" className="img" alt="描述" />
	<p style={{ color: 'red', backgroundColor: 'white' }}></p>
	<label htmlFor="user"></label>
	<input id="user" />
</>;
```

- class 要改为className
- style 要使用 JS 对象（不能是string） 而且key用驼峰写法
- for 要改为 htmlFor

### 2. 使用 JavaScript 表达式

在 JSX 中，可以使用大括号 `{}` 来嵌入 JavaScript 表达式：

```jsx
const name = "John";
const element = <h1>Hello, {name}!</h1>;
```

JSX注释内容

```jsx
{/* JSX注释内容 */}
```


## 监听 DOM 事件

在 React 中，我们可以轻松地为元素添加事件监听器。事件的命名方式与 JavaScript 的命名方式略有不同，使用小驼峰命名法。

### 1. 事件处理函数

以下是一个简单的按钮点击事件示例：

```jsx
function handleClick() {
  alert('按钮被点击！');
}

const element = <button onClick={handleClick}>点击我</button>;
```

### 2. 事件处理中的参数

如果需要向事件处理函数传递参数，可以使用箭头函数：

```jsx
function handleClick(name) {
  alert(`你好, ${name}!`);
}

const element = <button onClick={() => handleClick('John')}>点击我</button>;
```

### 3. 事件对象

在事件处理函数中，您可以访问事件对象：

```tsx
import type { MouseEvent } from 'react'
function handleClick(event: MouseEvent<HTMLButtonElement>, name: string) {
  event.preventDefault() // 阻止默认行为
  console.log('事件类型:', event.type, name);
}

const element = <button onClick={(event) => handleClick(event, 'elfecho')}>点击我</button>;
```

注意： 必须传入一个函数（是fn而非fn()）

## 条件判断

### 1. 使用三元运算符

三元运算符是进行条件判断的一种简洁方式。它的语法为：

javascript复制

`condition ? exprIfTrue : exprIfFalse`

示例
```tsx
const flag = true
{flag ? <p>hello</p> : <p>你好</p>}
```

### 2. 使用逻辑与运算符（&&）

逻辑与运算符可以用于简化条件渲染，特别是在只希望在某个条件为真时才渲染某个元素的情况下。

示例

```tsx
function Notification({ message }) {   
	return (     
		<div>       
			{message && <p>通知: {message}</p>}     
		</div>   
	); 
}
```

在这个例子中，只有在 `message` 非空时，才会渲染 `<p>` 标签。如果 `message` 是 falsy 值（例如 `null`、`undefined`、`false` 或空字符串），则不会渲染任何内容。

### 3. 使用 IIFE（立即调用函数表达式）

如果需要进行更复杂的逻辑判断，可以使用 IIFE 来处理条件。这种方法适用于需要执行多行逻辑的情况。

示例

```tsx
function UserStatus({ isLoggedIn }) {
  return (
    <div>
      {(() => {
        if (isLoggedIn) {
          return <h1>欢迎回来!</h1>;
        } else {
          return <h1>请登录!</h1>;
        }
      })()}
    </div>
  );
}

```

### 4. 其他条件渲染的方式

#### 4.1 使用 Switch 语句

在某些情况下，可能需要根据多个条件渲染不同的内容。此时可以使用 `switch` 语句。

jsx复制

```tsx
function UserRole({ role }) {
  let message;
  switch (role) {
    case 'admin':
      message = <h1>欢迎，管理员!</h1>;
      break;
    case 'user':
      message = <h1>欢迎，用户!</h1>;
      break;
    default:
      message = <h1>请登录!</h1>;
  }
  return <div>{message}</div>;
}

```
#### 4.2 使用数组的 map 方法

如果你需要渲染一个列表，可以使用 `map` 方法结合条件判断：

jsx复制

```tsx
function ItemList({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        item.isAvailable ? <li key={index}>{item.name}</li> : null
      ))}
    </ul>
  );
}

```

在这个例子中，我们使用 `map` 方法遍历 `items` 数组，并根据每个 `item` 的 `isAvailable` 属性判断是否渲染该项。

## 小结

JSX 是 React 开发的重要组成部分，了解其语法、标签、属性和事件监听的使用方法对于构建复杂的用户界面至关重要。通过使用 JSX，我们可以更直观地构建和组织组件，使得代码更易于理解和维护。

[typescript练习](http://tslang.cn/play/index.html)