# 实现vue项目改造服务端渲染



## vue-cli创建一个项目

在放你做demo的地方，创建一个项目

```bash
vue create vue-ssr
// 如果你安装了vue-cli4，选择vue2的版本，以下的改进过程是按vue2来做的1.2.
```



经过漫长的等待，下载好文件开始我们的改造之路

###  文件目录

进入vue-ssr文件夹，使用命令

```
vue ui
```

先安装几个依赖插件

```bash
// 安不上用cnpm，yarn，npx
npm i vue-server-renderer express webpack-node-externals lodash.merge cross-env -D
```



修改package.json文件

```json
"scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
}
改成
"scripts": {
    "build:client": "vue-cli-service build", 
    "build:server": "cross-env WEBPACK_TARGET=node vue-cli-service build --mode server", 
    "build": "npm run build:server && npm run build:client" 
}
```

根目录下创建vue.config.js

```javascript
// 服务器渲染的两个插件，控制server和client
const VueSSRServerPlugin = require("vue-server-renderer/server-plugin"); // 生成服务端包
const VueSSRClientPlugin = require("vue-server-renderer/client-plugin"); // 生成客户端包

const nodeExternals = require("webpack-node-externals");
const merge = require("lodash.merge");

// 环境变量：决定入口是客户端还是服务端，WEBPACK_TARGET在启动项中设置的，见package.json文件
const TARGET_NODE = process.env.WEBPACK_TARGET === "node";
const target = TARGET_NODE ? "server" : "client";

module.exports = {
  css: {
    extract: false
  },
  outputDir: "./dist/" + target,
  configureWebpack: () => ({
    // 将 entry 指向应用程序的 server / client 文件
    entry: `./src/entry-${target}.js`,
    // 对 bundle renderer 提供 source map 支持
    devtool: "source-map",
    // 这允许 webpack 以 Node 适用方式处理动态导入(dynamic import)，
    // 并且还会在编译 Vue 组件时告知 `vue-loader` 输送面向服务器代码(server-oriented code)。
    target: TARGET_NODE ? "node" : "web",
    node: TARGET_NODE ? undefined : false,
    output: {
      // 此处配置服务器端使用node的风格构建
      libraryTarget: TARGET_NODE ? "commonjs2" : undefined
    },
    // 外置化应用程序依赖模块。可以使服务器构建速度更快，并生成较小的 bundle 文件。
    externals: TARGET_NODE
      ? nodeExternals({
          // 不要外置化 webpack 需要处理的依赖模块。
          // 可以在这里添加更多的文件类型。例如，未处理 *.vue 原始文件，
          // 你还应该将修改 `global`（例如 polyfill）的依赖模块列入白名单（以前叫whitelist，为了避免美国的人种歧视，改成了allowlist）
          allowlist: [/\.css$/]
        })
      : undefined,
    optimization: {
      splitChunks: TARGET_NODE ? false : undefined
    },
    // 这是将服务器的整个输出构建为单个 JSON 文件的插件。
    // 服务端默认文件名为 `vue-ssr-server-bundle.json`
    // 客户端默认文件名为 `vue-ssr-client-manifest.json`
    plugins: [TARGET_NODE ? new VueSSRServerPlugin() : new VueSSRClientPlugin()]
  }),
  chainWebpack: config => {
    config.module
      .rule("vue")
      .use("vue-loader")
      .tap(options => {
        merge(options, {
          optimizeSSR: false
        });
      });
  }
};
```

修改路由文件

```javascript
import Vue from 'vue';
import Router from 'vue-router';
import Home from '../views/Home.vue';
import About from '../views/About.vue';

Vue.use(Router);

// 这里为什么不导出一个router实例？
// 每次用户请求都需要创建新router实例，如果用户请求多次都用一个实例会造成数据污染
export function createRouter() {
    return new Router({
        // 一定要history模式，因为，hash模式更改路径不会刷新，具体原因自行查询
        mode: 'history',
        routes: [
            {path: '/', name: 'Home',component: Home},
            {path: '/about', name: 'About', component: About},
        ]
    })
}
```

修改main.js文件

```javascript
import Vue from "vue";
import App from "./App.vue";
import { createRouter } from "./router";
import store from "./store";

Vue.config.productionTip = false;

const router = createRouter();

// 这里的挂载($mount("#app"))放到entry-client.js文件里面，后面会说到
export function createApp() {
    const app = new Vue({
        router,
        store,
        render: (h) => h(App),
    });
    return { app, router };
}
```

## 在src下添加entry-client.js和entry-server.js文件

### entry-client.js

```javascript
import {createApp} from './main.js';

const {app, router} = createApp();

router.onReady(()=>{
    app.$mount("#app");
})
```

### entry-server.js

```javascript
import {createApp} from "./main.js";
// context实际上就是server/index.js里面传参，后面会说到server/index.js
export default context => {
    return new Promise((resolve, reject) => {
        const {app, router} = createApp();
        router.push(context.url)
        router.onReady(()=>{
            // 是否匹配到我们要用的组件
            const matchs = router.getMatchedComponents();
            if(!matchs) {
                return reject({code: 404})
            }
            resolve(app);
        }, reject);
    })
}
```

### 在src下创建server/index.js

```javascript
// nodejs服务器
const express = require("express");
const fs = require("fs");

// 创建express实例和vue实例
const app = express();

// 创建渲染器
const { createBundleRenderer } = require("vue-server-renderer");
const serverBundle = require("../../dist/server/vue-ssr-server-bundle.json");
const clientManifest = require("../../dist/client/vue-ssr-client-manifest.json");
// 这儿引入的文件是不同于index.html的问题，具体文件下面会讲到
const template = fs.readFileSync("../../public/index.ssr.html", "utf-8"); // 宿主模板文件
const renderer = createBundleRenderer(serverBundle, {
    runInNewContext: false,
    template,
    clientManifest,
});

// 中间件处理静态文件请求
app.use(express.static("../../dist/client", { index: false })); // 为false是不让它渲染成dist/client/index.html
// app.use(express.static('../dist/client'))

// 前端请求什么我都不关心，所有的路由处理交给vue
app.get("*", async (req, res) => {
    try {
        const context = {
            url: req.url,
            title: "ssr test",
        };
        // nodejs流数据，文件太大，用renderToString会卡
        const stream = renderer.renderToStream(context);
        let buffer = [];
        stream.on("data", (chunk) => {
            buffer.push(chunk);
        });
        stream.on("end", () => {
            res.end(Buffer.concat(buffer));
        });
    } catch (error) {
        console.log(error);
        res.status(500).send("服务器内部错误");
    }
});

app.listen(3000, () => {
    console.log("渲染服务器启动成功");
});
```

在public下面创建index.ssr.html文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!--vue-ssr-outlet-->
    <!--上面这个一定要留着，它是服务端渲染模版的标记，没有就会报错，不信可以删了试一下-->
</body>
</html>
```

然后所有的改造完成，运行命令

```bash
// 先构建两个json文件
npm run build
```

再到server文件夹下运行

```bash
node index.js
// 如果显示: `渲染服务器启动成功`, 在浏览器打开 `localhost:3000` 端口，就能看到我们的页面
```