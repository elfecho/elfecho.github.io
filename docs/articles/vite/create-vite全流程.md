### 如何使用

根据官方文档，通过 `create-vite` 创建一个空白项目，只需要使用对应的 **包管理工具（NPM，Yarn，pNpm）** 执行 `create` 操作即可：
```go
npm create vite@latest
# or
yarn create vite
# or
pnpm create vite
```

执行后我们需要手动输入和确认 **项目名称、使用框架、语言类型**等，如果有和项目同名的文件夹，还会提示 **是否清空已有文件夹**。

当然，我们也可以直接 **将项目名和选择的模板** 作为参数直接拼接在命令后，例如：

```go
# npm 6.x
npm init vite@latest my-vue-app --template vue # npm 7+, 需要额外的双横线：npm init vite@latest my-vue-app -- --template vue 
# yarn
yarn create vite my-vue-app --template vue 
# pnpm
pnpm create vite my-vue-app -- --template vue
```