很多小伙伴苦于无法搭建一个规范的前端项目，导致后续开发不规范，今天给大家带来一个基于`Vite6+TypeScript+Vue3+Tailwind+ESlint+Prettier+Husky`的搭建教程

## 1. 初始化项目

1. `Node`版本要求：教程的 node 版本为 v22.17.0，`Vite`需要的 Node.js 版本 22+ ;
2. 创建项目

```javascript
# npm 10+，需要添加额外的 --： pnpm create vite@latest my-vue-app --template vue-ts
```

> 注意： Vite 需要  [Node.js](https://link.juejin.cn?target=https%3A%2F%2Fnodejs.org%2Fen%2F "https://nodejs.org/en/")  版本 22+。然而，有些模板需要依赖更高的 Node 版本才能正常运行，当你的包管理器发出警告时，请注意升级你的 Node 版本。

## 2.配置 TypeScript

1. 修改`package.json`

```json
"scripts": { 	
	"ts": "vue-tsc -b",  
}
```

2. 运行`pnpm ts` 即可检查文件中是否包含 ts 类型错误
    
    > 注意： 有时候遇到 ts 报是因为配置为生效，可以尝试重启 vscode 或 ts 服务（快捷键`ctrl+shift+p`调出命令行，输入`Restart TS Server`）

## 3.配置路径别名

1. 安装`@types/node`

```javascript
pnpm add @types/node -D
```

2. 修改`vite.config.ts`

```javascript
import { defineConfig } from 'vite';
import vue from "@vitejs/plugin-vue";
import path from 'path'; //引入path用到了上面安装的@types/node

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  //路径别名配置如下
  resolve: {
    alias: {
      '@': path.resolve('./src') // 用@代替src
    }
  }
});
```

3. 修改`tsconfig.app.json`

```json
{
	"compilerOptions":{
		...
		"baseUrl": ".", //设置基础路径
		"paths": { "@/*": ["src/*"] }, //路径映射
	}
}
```

## 4. antfu 组合 prettier & eslint

### 4.1 安装

```js
pnpm i -D eslint @antfu/eslint-config eslint-plugin-format
```

### 4.2 eslint.config.js

根目录配置新建eslint.config.js文件 用于eslint规则校验。

```JavaScript
import antfu from "@antfu/eslint-config";

export default antfu({ 
    // 1. 基本配置 
    type: "lib", // 表示当前项目是一个库（library），而不是应用（app） 
    // 2. 代码风格配置（Stylistic） 
    // stylistic: true, 
    // 或者你可以更加细粒度的设置 
    stylistic: { 
        indent: 2, // 缩进 2 个空格, 2 | 4 | 'tab' 
        quotes: "double", // 使用双引号, single | 'double' 
        semi: true, // 强制分号结尾, true | false 
        arrowParens: "always", // 箭头函数参数始终加括号，如 `(x) => x` 
        bracketSpacing: true, // 对象字面量括号加空格，如 `{ foo: bar }` 
        braceStyle: "1tbs", // 大括号换行风格（One True Brace Style） 
        commaDangle: "never", // 禁止尾随逗号 
        jsx: true, // 支持 JSX 语法 
        blockSpacing: true, // 代码块内加空格，如 `function foo() {}` 
        multilineComments: true, // 多行注释风格 
        preferArrowFunctions: true, // 优先使用箭头函数 

        // spaceBeforeFunctionParen: "always", // 函数名和参数括号间加空格 
        // spaceInParens: "never", // 括号内不加空格 
        // spacesInAngles: true, // 尖括号内加空格 
        // computedPropertySpacing: true, // 计算属性方括号内加空格 
        // restSpreadSpacing: true, // Rest/Spread 操作符周围加空格 
    }, 
    // 3. 格式化工具 
    formatters: true, // 使用外部格式化工具（如 Prettier） 
    
    // 4. 支持的语言特性 
    typescript: true, // 启用 TypeScript 支持 
    
    vue: { // Vue 文件的规则 
        overrides: { 
            // 强制 Vue 文件的顺序为 <template> -> <script> -> <style> 
            "vue/block-order": [ 
                "error", 
                { 
                    order: ["template", "script", "style"], 
                }, 
             ], 
         },
    }, 
    
    // 5. 规则覆盖（Rules） 
    /** 
    * 规则 https://www.wenjiangs.com/docs/eslint，vue规则：https://eslint.vuejs.org/rules/ 
    * 主要有如下的设置规则，可以设置字符串也可以设置数字，两者效果一致 
    * 'off' 或 0 - 关闭规则 
    * 'warn' 或 1 - 开启警告规则，使用警告级别的错误：warn (不会导致程序退出), 
    * 'error' 或 2 - 开启错误规则，使用错误级别的错误：error (当被触发的时候，程序会退出) 
    */ 
    rules: { 
        "no-console": "off", // 允许 console  
        // "no-debugger": "off", // 允许 debugger 
        "prefer-arrow-callback": "off", // 允许传统函数回调 
        // "func-style": "off", // 允许非箭头函数 
        // "@typescript-eslint/prefer-function-declarations": "off", // 关闭顶级函数必须用 function 声明的规则 
   },
   
   // 6. 忽略文件（Ignores） 
   // 9x版本 忽略文件这种配置方式 废弃掉eslintignore 
   // 【注意：ignores 是 @antfu/eslint-config 的配置方式，替代了传统的 .eslintignore 文件】 
   ignores: [ 
       "*.sh", 
       "node_modules", 
       "*.md", 
       "*.woff", 
       "*.ttf", 
       ".vscode", 
       ".idea", 
       "/public", 
       "/docs", 
       ".husky", 
       ".local", 
       "/bin", 
       "Dockerfile", 
       "/dist", 
       "/src/libs", 
       "src/mock", 
       "*.min.js", 
       "/*.json", 
    ], 
    // 7. 其他配置 
    // 关闭对 JSON 和 YAML 的支持 
    jsonc: false, // 关闭 JSON 支持 
    yaml: false, // 关闭 YAML 支持 
    // isInEditor: false, // 保存删除未引入的代码 
    // unocss: true, // unocss 检测&格式化 暂时注释 等配置了unocss再开放为true 
});
```

### 4.3 VS Code settings.json （*最终版本的 settings.json*）

用于保存带代码格式化 
 配置已完成 当文件保存的时候即可格式化。

```JSON
{
    // Enable the ESlint flat config support
    "eslint.experimental.useFlatConfig": true,
    // Disable the default formatter, use eslint instead
    "prettier.enable": false,
    "editor.formatOnSave": false,
    // Auto fix
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": "explicit",
        "source.organizeImports": "never",
        "source.fixAll.stylelint": "explicit",
        "source.fixAll": "explicit"
    },
    // stylelint配置
    "stylelint.enable": true,
    // 关闭编辑器内置样式检查（避免与stylelint冲突）
    "css.validate": false,
    "less.validate": false,
    "scss.validate": false,
    "stylelint.validate": [
        "css",
        "less",
        "postcss",
        "scss",
        "sass",
        "vue"
    ],
    // Silent the stylistic rules in you IDE, but still auto fix them
    "eslint.rules.customizations": [
        {
            "rule": "style/*",
            "severity": "off"
        },
        {
            "rule": "format/*",
            "severity": "off"
        },
        {
            "rule": "*-indent",
            "severity": "off"
        },
        {
            "rule": "*-spacing",
            "severity": "off"
        },
        {
            "rule": "*-spaces",
            "severity": "off"
        },
        {
            "rule": "*-order",
            "severity": "off"
        },
        {
            "rule": "*-dangle",
            "severity": "off"
        },
        {
            "rule": "*-newline",
            "severity": "off"
        },
        {
            "rule": "*quotes",
            "severity": "off"
        },
        {
            "rule": "*semi",
            "severity": "off"
        }
    ],
    // Enable eslint for all supported languages
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "typescript",
        "typescriptreact",
        "vue",
        "html",
        "markdown",
        "json",
        "jsonc",
        "yaml",
        "toml"
    ]
}
```

### 4.4 新增脚本package.json

```JSON
"scripts": {
       // ...
      "lint": "eslint .",
      "lint:fix": "eslint . --fix"
    }
```

## 5. styleLint

### 5.1 安装

```shell
# 如果安装 sass(默认)
pnpm i postcss postcss-html postcss-scss sass stylelint stylelint-config-recess-order stylelint-config-standard stylelint-prettier stylelint-config-prettier -D

# 如果安装 less
pnpm i postcss postcss-html postcss-less less stylelint stylelint-config-recess-order stylelint-config-standard stylelint-prettier stylelint-config-prettier -D
# less + sass
pnpm i less sass postcss postcss-html postcss-less postcss-scss sass stylelint stylelint-config-recess-order stylelint-config-standard stylelint-prettier stylelint-config-prettier -D
```

- stylelint-config-rational-order 处理css属性间的顺序

### 5.2 根目录新建文件 stylelint.config.js

```js
export default {
  // 1. 基础配置，继承两个官方推荐的配置
  // stylelint-config-standard：Stylelint 的标准规则集，涵盖常见的 CSS 规范。
  // stylelint-config-recess-order：强制 CSS 属性按照特定顺序排列（如 position -> display -> margin 等），提高代码可读性。
  extends: ["stylelint-config-standard", "stylelint-config-recess-order", "stylelint-prettier/recommended"],

  // 2. 文件解析器
  overrides: [
    {
      files: ["**/*.(css|html|vue)"],
      customSyntax: "postcss-html", // 解析 CSS、HTML、Vue(如 Vue 的 <style> 块) 文件
    },
    // 选less可以注释scss
    {
      files: ["*.less", "**/*.less"],
      customSyntax: "postcss-less", // 解析 Less 文件
    },
    // 选sass可以注释上面的less
    {
      files: ["*.scss", "**/*.scss"],
      customSyntax: "postcss-scss", // 解析 SCSS 文件
      rules: {
        "scss/percent-placeholder-pattern": null, // 关闭 SCSS 占位符检查
        "scss/at-mixin-pattern": null, // 关闭 SCSS Mixin 命名检查
      },
    },
  ],

  // 3. 自定义规则
  rules: {
    // (1) 忽略某些规则: 这些规则被设为 null，表示不检查，通常是为了兼容项目特殊情况。
    "media-feature-range-notation": null, // 允许 `min-width: 768px` 或 `width >= 768px`
    "selector-not-notation": null, // 允许 `:not(.class)` 或 `:not(.class, .other)`
    "import-notation": null,
    "function-no-unknown": null, // 允许未知的 CSS 函数（如自定义函数）
    "selector-class-pattern": null, // 允许任意类名（默认强制 kebab-case）
    "no-empty-source": null, // 允许空样式文件
    "no-descending-specificity": null, // 允许低优先级选择器覆盖高优先级
    "font-family-no-missing-generic-family-keyword": null, // 允许 `font-family: Arial`（不强制加 `sans-serif`）
    "named-grid-areas-no-invalid": null, //  禁用对 CSS Grid 命名区域有效性的检查。

    // (2) 特殊伪类/伪元素处理: 允许 Vue 特有的伪类（如 :global）和伪元素（如 ::v-deep），避免误报。
    "selector-pseudo-class-no-unknown": [
      true,
      {
        ignorePseudoClasses: ["global", "deep"], // 允许 `:global` 和 `:deep`（Vue Scoped CSS）
      },
    ],
    "selector-pseudo-element-no-unknown": [
      true,
      {
        ignorePseudoElements: ["v-deep", ":deep"], // 允许 `::v-deep` 和 `::deep`（Vue 深度选择器）
      },
    ],

    // (3) 忽略未知的 @ 规则: 允许 TailwindCSS、SCSS/Less 的特殊语法（如 @apply、@mixin）
    "at-rule-no-unknown": [
      true,
      {
        ignoreAtRules: [
          // TailwindCSS
          "tailwind",
          "apply",
          "variants",

          // SCSS/Less 控制语句
          "function",
          "if",
          "each",

          // SCSS Mixins
          "include",
          "mixin",
          "extend",

          // "use",
          // "responsive",
          // "screen",
        ],
      },
    ],

    // (4) 其他重要规则
    "unit-no-unknown": [true, { ignoreUnits: ["rpx"] }], // 允许微信小程序的 `rpx` 单位
    "rule-empty-line-before": [// 规则前空行（注释后除外）
      "always",
      {
        ignore: ["after-comment", "first-nested"],
      },
    ],
    "property-no-vendor-prefix": [ // 允许 `-webkit-` 前缀
      true,
      {
        ignoreProperties: ["background-clip"],
      },
    ],

    // (5) CSS 属性顺序（重要！）
    // "order/order": [
    //   [
    //     "dollar-variables", // $variables
    //     "custom-properties", // --css-vars
    //     "at-rules", // @media, @keyframes
    //     "declarations", // 普通 CSS 属性
    //     { type: "at-rule", name: "supports" }, // @supports
    //     { type: "at-rule", name: "media" }, // @media
    //     "rules", // 嵌套规则
    //   ],
    //   { severity: "error" }, // 违反时报错（非警告）
    // ],
  },

  // 4. 忽略文件: 不检查 .js、.jsx、.tsx、.ts 文件（Stylelint 只处理样式文件）
  ignoreFiles: ["**/*.js", "**/*.jsx", "**/*.tsx", "**/*.ts"],
};
```

### 5.3 新增脚本package.json

```js
{
    "scripts": {
        // ...
        "lint:stylelint": "stylelint  \"**/*.{css,scss,less,vue,html}\" --fix"
    }
}
```

### 5.4 settings.json （最终版本的 settings.json）

下载stylelint插件后配置settings.json, 在settings.json中添加配置项 这里是配置的全局，也可参考配置只针对当前项目

```JSON
JSON 体验AI代码助手 代码解读复制代码{
    // Enable the ESlint flat config support
    "eslint.experimental.useFlatConfig": true,
    // Disable the default formatter, use eslint instead
    "prettier.enable": false,
    "editor.formatOnSave": false,
    // Auto fix
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": "explicit",
        "source.organizeImports": "never",
        "source.fixAll.stylelint": "explicit",
        "source.fixAll": "explicit"
    },
    // stylelint配置
    "stylelint.enable": true,
    // 关闭编辑器内置样式检查（避免与stylelint冲突）
    "css.validate": false,
    "less.validate": false,
    "scss.validate": false,
    "stylelint.validate": [
        "css",
        "less",
        "postcss",
        "scss",
        "sass",
        "vue"
    ],
    // Silent the stylistic rules in you IDE, but still auto fix them
    "eslint.rules.customizations": [
        {
            "rule": "style/*",
            "severity": "off"
        },
        {
            "rule": "format/*",
            "severity": "off"
        },
        {
            "rule": "*-indent",
            "severity": "off"
        },
        {
            "rule": "*-spacing",
            "severity": "off"
        },
        {
            "rule": "*-spaces",
            "severity": "off"
        },
        {
            "rule": "*-order",
            "severity": "off"
        },
        {
            "rule": "*-dangle",
            "severity": "off"
        },
        {
            "rule": "*-newline",
            "severity": "off"
        },
        {
            "rule": "*quotes",
            "severity": "off"
        },
        {
            "rule": "*semi",
            "severity": "off"
        }
    ],
    // Enable eslint for all supported languages
    "eslint.validate": [
        "javascript",
        "javascriptreact",
        "typescript",
        "typescriptreact",
        "vue",
        "html",
        "markdown",
        "json",
        "jsonc",
        "yaml",
        "toml"
    ]
}
```

### 5.5 设置 stylelint 忽略文件 根目录新增文件.stylelintignore

```JSON
/dist/*
/public/*

node_modules
.husky
.vscode
.idea
*.sh
*.md

stats.html
```

## 6. husky 用于代码提交

### 5.1 安装

```js
官网Husky 推荐安装指令
1. 前提条件 项目有.git，有git的话不需要这一步；如果没有需要生成, 执行: git init
2. 自动配置husky, 执行: npx husky-init
3. 安装husky 执行: npm i
```

### 5.2 Lint-staged 增量检测提交代码

#### 5.2.1 lint-staged 是一个在 git add 到暂存区的文件运行 linters (ESLint/Prettier/StyleLint) 的工具，避免在 git commit 提交时在整个项目执行。

```
pnpm i lint-staged -D
```

#### 5.2.2 新建lint-staged.config.js配置文件

```js
/**  @type {import('lint-staged').Config} */
export default {
  "*.{js,jsx,ts,tsx}": ["eslint --fix && git add"],
  "*.json": ["prettier --write && git add"], // JSON 文件检查 ：JSON 文件通常使用 prettier 进行格式化
  "*.vue": ["eslint --fix && git add"],
  "*.{scss,less,styl,html}": ["stylelint --fix --allow-empty-input && git add"],
  "*.md": ["prettier --write && git add"],
};
```

#### 5.2.3 新增脚本package.json

```
"lint:lint-staged": "lint-staged"
```

#### 5.2.4 文件.husky/pre-commit 修改提交前钩子命令，npx命令会自动执行安装过的 lint-staged插件，从而执行lint-staged.config.js配置文件

```js
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# npm test

npm run lint:lint-staged --allow-empty
```

### 5.3 Commitlint: 检查您的提交消息是否符合 Conventional commit format。

#### 5.3.1 安装

```
pnpm i @commitlint/cli @commitlint/config-conventional -D
```

#### 5.3.2 根目录创建 commitlint.config.js 配置文件

```js
/** @type {import("@commitlint/types").UserConfig} */
export default {
  ignores: [(commit) => commit.includes("init")],
  extends: ["@commitlint/config-conventional"],
  rules: {
    "body-leading-blank": [2, "always"],
    "footer-leading-blank": [1, "always"],
    "header-max-length": [2, "always", 108],
    "subject-empty": [2, "never"],
    "type-empty": [2, "never"],
    "subject-case": [0],
    "type-enum": [
      2,
      "always",
      [
        "feat", // 新增功能
        "fix", // 修复缺陷
        "docs", // 文档变更
        "style", // 代码格式（不影响功能，例如空格、分号等格式修正）
        "refactor", // 代码重构（不包括 bug 修复、功能新增）
        "perf", // 性能优化
        "test", // 添加疏漏测试或已有测试改动
        "build", // 构建流程、外部依赖变更（如升级 npm 包、修改 webpack 配置等）
        "ci", // 修改 CI 配置、脚本
        "revert", // 回滚 commit
        "chore", // 对构建过程或辅助工具和库的更改（不影响源文件、测试用例）
      ],
    ],
  },
};
```

#### 5.3.3 执行下面命令生成 commint-msg 钩子用于 git 提交信息校验

```js
npx husky add .husky/commit-msg "npx --no-install commitlint --edit $1"

//  自动生成 .husky/commit-msg 文件， commit-msg 文件如下
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"
npx --no-install commitlint --edit $1
```

#### 5.3.4 commit：(提交的时候需要使用)

```shell
git add .
git commit -m 'feat: 新增xx功能'

- `feat` 增加新功能
- `fix` 修复问题/BUG
- `style` 代码风格相关无影响运行结果的
- `perf` 优化/性能提升
- `refactor` 重构
- `revert` 撤销修改
- `test` 测试相关
- `docs` 文档/注释
- `chore` 依赖更新/脚手架配置修改等

注意：feat后的冒号是英文的格式，其后必须加一个英文空格
```

#### 5.3.5 commit：(提交的时候检测代码中是否包含 debugger )

创建文件：.husky/check-keyword.sh

```shell
#!/bin/bash

echo "》》》开始检查暂存区代码是否有 'debugger'..."

for FILE in $(git diff --name-only --cached -- 'src/')
do
    # 这是一个正则表达式，用来匹配 debugger
    if grep 'debugger' "$FILE";
    then
        echo "《《《$FILE 包含 'debugger'，请删除后再提交！"
        exit 1
    fi
done

echo "《《《恭喜你，检测通过！"
exit
```

修改 .husky/pre-commit

```js
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# npm test
# npm run lint:lint-staged --allow-empty
pnpm run lint:lint-staged --allow-empty && pnpm run check-keyword
```

新增脚本package.json

```
"check-keyword": "bash .husky/check-keyword.sh"
```

> 注意：如果使用vscode图形界面进行提交git代码，会出现没有拦截commitlint的情况，可以在`.vscode/settings.json`下添加这个配置

```
"git.useCommitInputAsStashMessage": true
```


