在现代前端开发中，React 是一个非常流行的 JavaScript 库，用于构建用户界面。为了快速启动一个 React 项目，Facebook 提供了一个名为 Create React App 的工具，它可以帮助开发者轻松地创建一个配置好的 React 开发环境。本文将介绍如何使用 Create React App 快速搭建 React 开发环境。

## 为什么选择 Create React App？

1. **简单易用**：Create React App 提供了开箱即用的配置，开发者可以专注于编写代码，而无需担心复杂的构建工具配置。
2. **内置功能**：它包含了许多现代开发所需的功能，如热重载、代码分割、Linting、测试框架等。
3. **社区支持**：Create React App 拥有活跃的社区支持，遇到问题时可以轻松找到解决方案。

## 环境准备

在开始之前，请确保你的开发环境中已经安装了 Node.js 和 npm。你可以通过以下命令检查版本：

```bash
node -v
npm -v
```

如果你还没有安装 Node.js，可以前往 [Node.js 官网](https://nodejs.org/) 下载并安装。

### 步骤 1: 创建新的 React 应用

使用 Create React App 创建新的 React 项目非常简单。打开终端，运行以下命令：

```bash
npx create-react-app my-app
# typescript环境
npx create-react-app my-app --template typescript
```

这里的 `my-app` 是你想要创建的应用名称。你可以将其替换为任何你想要的名称。

**注意**：`npx` 是 npm 5.2+ 中引入的工具，它允许你运行 npm 包而无需全局安装。使用 `npx` 可以确保你使用的是最新版本的 Create React App。

#### 更改react版本

指定期望的react版本，因为我需要指定react16，且不需要指定具体小版本，所以使用16.x

```bash
npm i react-dom@16.x --legacy-peer-deps
npm i react@16.x --legacy-peer-deps

# 切换到react18
npm i react-dom@18.x react@18.x --legacy-peer-deps
```

执行以上命令会自动更新package.json中react-dom和react的版本号以及node_modules中的依赖包，无需手动修改删除。

最后修改项目根目录-src-index.js，react18的根节点创建和渲染写法与之前略有不同，降级后会报错，修改一下。

### 步骤 2: 进入项目目录

创建完成后，进入到刚创建的项目目录：

```bash
cd my-app
```

### 步骤 3: 启动开发服务器

在项目目录中，运行以下命令启动开发服务器：

```bash
npm start
```

此时，Create React App 将启动一个开发服务器，并在默认浏览器中打开 `http://localhost:3000`。你应该能够看到一个默认的 React 欢迎页面。

### 步骤 4: 编辑你的第一个组件

现在你可以开始编辑代码了！打开项目中的 `src` 文件夹，你会看到一个名为 `App.js` 的文件。这个文件是你应用的主组件。

用你喜欢的代码编辑器打开 `App.js`，并进行以下更改：

```javascript
import React from 'react';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <h1>Hello, React!</h1>
        <p>
          Welcome to your first React application.
        </p>
      </header>
    </div>
  );
}

export default App;
```

保存文件后，浏览器会自动刷新，显示你所做的更改。

### 步骤 5: 其他常用命令

Create React App 提供了一些有用的命令，可以帮助你更好地管理项目：

- **构建项目**：当你准备好将应用部署到生产环境时，可以运行以下命令：

  ```bash
  npm run build
  ```

  这会创建一个优化过的生产版本，输出到 `build` 文件夹。

- **运行测试**：Create React App 还内置了 Jest 测试框架来帮助你编写和运行测试：

  ```bash
  npm test
  ```

- **弹出配置**：如果你需要自定义 webpack 配置，可以使用以下命令弹出配置（注意：这将使配置更复杂）：

  ```bash
  npm run eject
  ```

### 步骤 6: 安装额外的依赖

在开发过程中，你可能需要安装一些额外的依赖，例如路由、状态管理等。下面是一些常用的库及其安装命令：

- **React Router**（用于路由管理）：

  ```bash
  npm install react-router-dom
  ```

- **Redux**（用于状态管理）：

  ```bash
  npm install redux react-redux
  ```


## 安装 eslint 与 prettierrc

使用`create-react-app`创建的项目，默认是没有eslint校验的，然后想加上eslint校验，因为示例代码每行后面都有一个分号，以及使用双引号等，跟之前的习惯向左，所以通过eslint校验并格式化代码。

首先要在当前项目安装eslint

```sh
npm i eslint -D 
yarn add eslint -D 
pnpm add -D eslint
```

我安装的版本是`^8.57.1。

然后在当前目录下初始化eslint配置

```sh
// 通过npx执行
eslint npx eslint -init 
// 也可以通过下面命令 
node ./node_modules/.bin/eslint --init
```

都是去bin目录执行eslint的可执行文件

然后就会问你的eslint的配置选项
![[Pasted image 20250225170555.png]]
后面会生成一个 `eslint.config.mjs` 文件

安装prettier
```sh
npm install prettier eslint-config-prettier eslint-plugin-prettier --save-dev
```

在根目录新建一个 `.prettierrc.js`文件

```javascript
module.exports = {
  //箭头函数只有一个参数的时候可以忽略括号
  arrowParens: 'avoid',
  //括号内部不要出现空格
  bracketSpacing: true,
  //行结束符使用 Unix 格式
  endofLine: 'lf',
  // true: Put >on the last line instead of at a new line
  jsxBracketSameLine: false,
  // 行宽
  printwidth: 100,
  //换行方式
  proseWrap: 'preserve',
  // 分号
  semi: false,
  // 使用单引号
  singleQuote: true,
  // 缩进
  tabWidth: 2,
  // 使用 tab 缩进
  useTabs: false,
  //后置逗号，多行对象、数组在最后一行增加逗号
  trailingComma: 'es5',
  parser: 'typescript'
}
```
