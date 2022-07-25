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

![upgit_20220725_1658723845.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658723845.png)

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
* [home1](home1)
* [home2](home2)
* [bar](bar/)
* [bar/a](bar/a)
```