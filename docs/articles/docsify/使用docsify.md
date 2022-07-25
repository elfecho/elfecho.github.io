## docsify介绍

docsify 是一个动态生成文档网站的工具。不同于 GitBook、Hexo 的地方是它不会生成将 .md 转成 .html 文件，所有转换工作都是在运行时进行。

这将非常实用，如果只是需要快速的搭建一个小型的文档网站，或者不想因为生成的一堆 .html 文件“污染” commit 记录，只需要创建一个 index.html 就可以开始写文档而且直接部署在 GitHub Pages

## 引入docsify方式

### 手动创建`index.html`并引入docsify文件

docsify使用方式很简单，只需要在项目中创建一个`index.html`文件，内容能够如下：

```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//unpkg.com/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

然后在项目中创建一个`README.md`文件：

```markdown
## 我是首页

这是我的首页介绍
```

可以通过`http-server`启动服务
可在终端输入`npm install http-server -g` 来安装`http-server`

```bash
http-server -p 8080
```
**_注意：_**  
1.在此模式下编辑文件保存后，需要手动刷新浏览器才能看见修改的效果，下面介绍的`docsify-cli`可实现自动查看效果。  
2.强烈建议把`index.html`文件中的`docsify.min.js`和`vue.css`文件复制到本地项目，然后使用如下方式引入：

```html
<link rel="stylesheet" href="./vue.css">
<script src="./docsify.min.js"></script>
```

这样做的好处是不在依赖网络环境了

### 使用`docsify-cli`来开发

docsify需要本地先安装`node`, 如果没有安装node，可在node官网选择对应操作系统下载安装：[https://nodejs.org/zh-cn/](https://link.segmentfault.com/?enc=I8jTglj86w1fROL0FayGUA%3D%3D.1E8IvNhFLfemIs1alsa3niSlF7%2BgNkmKF%2FdgAXkFEwE%3D)

全局安装

```bash
npm i docsify-cli -g
```

安装结束后使用`docsify -v`查看是否安装成功：

```bash
docsify -v
```

#### 初始化一个项目

首先需要创建一个项目目录：

```bash
mkdir docsify
```

进入项目目录后，使用`docsify init ./`来初始化一个项目：

```bash
cd docsify

docsify init ./
```

初始化成功后,`docsify`目录会生成如下几个文件：

> 1.  `index.html`入口文件
> 2.  `README.md`会做为主页内容渲染
> 3.  `.nojekyll`用于阻止 GitHub Pages 会忽略掉下划线开头的文件、
> 
> `.nojekyll`文件很重要，如果网站部署到`GitHub Pages`时，一定要注意这个文件

直接编辑 ./README.md 就能更新网站内容，当然也可以添加其他`.md`文件

#### 启动本地服务

终端输入`docsify serve ./`来启动服务：

```bash
docsify serve ./

Serving /Users/dragon/tmp/docsify now.
Listening at http://localhost:3000
```

然后浏览器打开`http://localhost:3000`就能看见效果

![upgit_20220725_1658724123.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658724123.png)


当修改文件保存后， `docsify serve ./`服务会自动实时更新

## 关于每个页面和URL路径说明

如果需要创建多个页面，或者需要多级路由的网站，在`docsify`里也能很容易的实现。例如创建一个`guide.md`文件，那么对应的路由就是`/#/guide`。
如果你的目录结构如下：

```1c
-| ./
  -| README.md
  -| guide.md
  -| zh-cn/
    -| README.md
    -| guide.md
```

那么对应的访问页面将是:

```dart
./README.md        => http://localhost:3000
./guide.md         => http://localhost:3000/guide
./zh-cn/README.md  => http://localhost:3000/zh-cn/
./zh-cn/guide.md   => http://localhost:3000/zh-cn/guide
```

## 侧边栏设置

默认情况下，侧边栏会根据当前文档的标题生成目录。

![upgit_20220725_1658724722.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658724722.png)


### 定制侧边栏

首先需要在`index.html`文件中的`window.$docsify`添加`loadSidebar: true,`选项：

```xml
<script>
  window.$docsify = {
    loadSidebar: true
  }
</script>
<script src="//unpkg.com/docsify"></script>
```

接着在项目根目录创建`_sidebar.md`文件，内容格式如下：

```markdown
* [home](home)
* [guide](guide)
* [bar/a](bar/a)
* [bar/b](bar/b)
```

**_注_**: 配置了`loadSidebar`后就不会生成默认的侧边栏了

### 关于侧边栏`_sidebar.md`文件的说明

-   如果只在根目录有一个`_sidebar.md`文件，那么所有页面都将使用这个一个配置，也就是所有页面的侧边栏都一样。
-   如果一个子目录中有`_sidebar.md`文件，那么这个子目录下的所有页面将使用这个文件的侧边栏。
-   `_sidebar.md`的加载逻辑是从每层目录下获取文件，如果当前目录不存在该文件则回退到上一级目录。例如当前路径为`/zh-cn/more-pages`则从`/zh-cn/_sidebar.md`获取文件，如果不存在则从`/_sidebar.md`获取。

如果子目录有`_sidebar.md`,但你就想使用根目录的`_sidebar.md`，  
可在`index.html`文件中的`window.$docsify`添加`alias`字段：

```javascript
window.$docsify = {
    loadSidebar: true,
    alias: {
        '/.*/_sidebar.md': '/_sidebar.md'
    }
}
```

配置`alias`字段后，所有页面都会显示项目根目录`_sidebar.md`文件的配置作为侧边栏，子目录的`_sidebar.md`文件会失效。

### 显示页面目录(当前页面的标题)

定制的侧边栏仅显示了页面的链接。
还可以设置在侧边栏显示当前页面的目录(标题)。
需要在`index.html`文件中的`window.$docsify`添加`subMaxLevel`字段来设置：

```javascript
<script>
  window.$docsify = {
    loadSidebar: true,
    subMaxLevel: 3
  }
</script>
<script src="//unpkg.com/docsify"></script>
```

![upgit_20220725_1658726908.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658726908.png)

subMaxLevel说明：

> subMaxLevel类型是number(数字)，表示显示的目录层级
> **注意：**如果md文件中的第一个标题是一级标题，那么不会显示在侧边栏，如上图所示

| 值   | 说明                                           |
| ---- | ---------------------------------------------- |
| 0    | 默认值，表示不显示目录                         |
| 1    | 显示一级标题(`h1`)                             |
| 2    | 显示一、二级标题(`h1` ~ `h2`)                  |
| 3    | 显示一、二、三级标题(`h1` ~ `h3`)              |
| n    | n是数字，显示一、二、....n 级标题(`h1` ~ `hn`) |

在md文件中标题的写法：

```markdown
# 这是一级标题，对应HTML中<h1>标签

## 这是二级标题，对应HTML中<h2>标签

### 这是三级标题，对应HTML中<h3>标签

#### 这是四级标题，对应HTML中<h4>标签
```

#### 页面的标题不在侧边栏目录显示

***注意：\*** 如果md文件的第一个标题是一级标题，那么默认已经忽略了。

当设置了 subMaxLevel 时，默认情况下每个标题都会自动添加到目录中。如果你想忽略特定的标题，可以给它添加 {docsify-ignore} ：

```markdown
# Getting Started

## Header {docsify-ignore}

该标题不会出现在侧边栏的目录中。
```

要忽略特定页面上的所有标题，你可以在页面的第一个标题上使用 {docsify-ignore-all} :

```markdown
# Getting Started {docsify-ignore-all}

## Header

该页面所有标题都不会出现在侧边栏的目录中。
```

在使用时， {docsify-ignore} 和 {docsify-ignore-all} 都不会在页面上显示。

## 导航栏配置

docsify默认是没有导航栏的，可以通过配置来显示导航栏。

### 在`index.html`中定义导航栏

如果导航的链接少，则可以直接在`index.html`文件直接定义导航栏，要注意链接要以`#/`开头：

```xml
<body>
  <nav>
    <a href="#/">项目</a>
    <a href="#/home1">home1</a>
    <a href="#/bar/a">bar/a</a>
  </nav>
</body>
```

![upgit_20220725_1658727540.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658727540.png)

### 通过配置文件定义导航栏

首先需要在`index.html`文件中的`window.$docsify`添加`loadNavbar: true,`选项：

```xml
<script>
  window.$docsify = {
    loadNavbar: true
  }
</script>
<script src="//unpkg.com/docsify"></script>
```

接着在项目根目录创建`_navbar.md`文件，内容格式如下：

```markdown
* [home](home)
* [bar](bar/)
* [bar/a](bar/a)
```

![upgit_20220725_1658729036.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658729036.png)

***注意：\***
1. 如果使用配置文件来设置导航栏，那么在`index.html`中定义的导航栏只有在定制的首页才会生效，其他页面会被覆盖。
2. 如果只在根目录有一个`_navbar.md`文件，那么所有页面都将使用这个一个配置，也就是所有页面的导航栏都一样。
3. 如果一个子目录中有`_navbar.md`文件，那么这个子目录下的所有页面将使用这个文件的导航栏。
4. `_navbar.md`的加载逻辑是从每层目录下获取文件，如果当前目录不存在该文件则回退到上一级目录。例如当前路径为`/zh-cn/more-pages`则从`/zh-cn/_navbar.md`获取文件，如果不存在则从`/_navbar.md`获取。

### 导航栏嵌套

如果导航内容过多，可以写成嵌套的列表，会被渲染成下拉列表的形式：

```markdown
* 根目录
  * [home](home)
  * [guide](guide)

* bar目录
  * [bar](bar/)
  * [a文件](bar/a)
  * [b文件](bar/b)

* foo目录
  * [one](foo/one)
  * [two](foo/two)
```

![upgit_20220725_1658729486.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658729486.png)

## 设置封面

docsify默认是没有封面的，默认有个首页`./README.md`。
通过设置`coverpage`参数，可以开启渲染封面的功能。

首先需要在`index.html`文件中的`window.$docsify`添加`coverpage: true`选项：

```xml
<script>
  window.$docsify = {
    coverpage: true
  }
</script>
<script src="//unpkg.com/docsify"></script>
```

接着在项目根目录创建`_coverpage.md`文件，内容格式如下：

```markdown
![logo](_media/logo.png)
# 我的文档网站
## 个人文档网站
> 一个神奇的文档网站生成巩固

* Simple and lightweight (~12kb gzipped)
* Multiple themes
* Not build static html files

[GitHub](https://github.com/docsifyjs/docsify/)
[Get Started](/README)
```

![upgit_20220725_1658731279.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658731279.png)

