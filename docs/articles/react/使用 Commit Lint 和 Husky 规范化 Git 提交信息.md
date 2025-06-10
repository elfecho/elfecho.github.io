在现代软件开发中，良好的代码管理和提交信息的规范化是团队协作中至关重要的一部分。为了保持提交信息的清晰与一致，许多团队选择使用 Commit Lint 工具与 Husky 结合，以强制执行提交信息的格式。本文将详细介绍如何设置和使用 Commit Lint 与 Husky，从而规范化你的 Git 提交信息。

## 什么是 Commit Lint？

Commit Lint 是一个用于检查 Git 提交信息格式的工具。它能够根据约定的规则（如 Conventional Commits）来验证提交信息的格式，确保所有提交信息在项目中的一致性，从而提高代码的可读性和可维护性。

### 为什么使用 Commit Lint？

1. **一致性**：通过统一的提交信息格式，所有开发者都能更容易理解每次提交的目的。
2. **自动化**：可以与 CI/CD 工具集成，自动检查提交信息，避免手动检查的麻烦。
3. **生成变更日志**：使用标准格式提交信息可以轻松生成变更日志，便于版本管理和发布。

## 什么是 Husky？

Husky 是一个 Git hooks 工具，可以让你在 Git 提交、合并等操作时自动执行脚本。结合 Commit Lint，Husky 可以确保所有提交信息在推送到版本库之前都经过格式检查。

### 为什么使用 Husky？

1. **自动化流程**：自动化执行检查，减少人为错误。
2. **提高效率**：通过在提交前进行检查，确保代码质量。
3. **轻松集成**：与现有的开发流程无缝集成。

## 如何使用 Commit Lint 和 Husky

### 步骤 1: 安装依赖

首先，在你的项目中安装 Commit Lint 和 Husky。运行以下命令：

```bash
# npm(windows)
npm install --save-dev @commitlint/config-conventional @commitlint/cli husky
# npm(Mac)
npm install --save-dev @commitlint/{cli,config-conventional} husky
```

### 步骤 2: 创建 Commit Lint 配置文件

在项目根目录下创建一个名为 `commitlint.config.js` 的文件，执行以下命令即可

```javascript
echo "export default { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
```

这个配置文件指定了使用 Conventional Commits 规范。

### 步骤 3: 初始化 Husky

启用 Husky，并创建 Git hooks。运行以下命令：

```bash
# v8
npx husky install
```

然后添加一个 `commit-msg` 钩子，确保在每次提交时检查提交信息格式：

```bash
# v8
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit ${1}'
```

Add a hook:

```shell
# v8
npx husky add .husky/pre-commit "npm run text"
git add .husky/pre-commit

```

Make a commit:

```shell
git commit -m "Keep calm and commit"
# `npm test` will run every time you commit
```

### 步骤 4: 提交信息格式

现在你已经配置好了 Commit Lint 和 Husky，接下来我们来看看如何编写符合规范的提交信息。根据 Conventional Commits 规范，提交信息应该包括以下部分：

1. **类型**：一个小写字母，表示提交的类型，例如：
   - `feat`：新功能
   - `fix`：修复 bug
   - `docs`：文档变更
   - `style`：格式（不影响代码运行的变更）
   - `refactor`：重构（即不是新增功能，也不是修复 bug 的代码变动）
   - `test`：增加测试
   - `chore`：其他变动（例如构建过程或辅助工具的变动）

2. **范围**（可选）：可以在类型后加上小括号，指定影响的模块或范围，例如：`feat(core): add new feature`。

3. **主题**：简短明了地描述本次提交的内容。

### 示例提交信息

以下是一些符合 Commit Lint 规范的提交信息示例：

```
feat(user): add user login functionality
fix(api): correct the user data fetching bug
docs(README): update installation instructions
style(button): improve button color contrast
refactor(auth): simplify authentication middleware
test(user): add test cases for user model
chore(deps): update dependency versions
```

### 步骤 5: 提交代码

当你进行 Git 提交时，遵循 Commit Lint 的格式规范，示例命令如下：

```bash
git add .
git commit -m "feat(user): add user login functionality"
```

如果提交信息不符合规范，Husky 会阻止提交并输出错误信息，你需要根据提示修改提交信息。

##  Husky(husky9.x版本适配) + Lint-staged + Commitlint + Commitizen  配置 Git 提交规范

### Husky安装命令：

```bash
npm install --save-dev husky

npx husky init
```
### Commitlint

Commitlint 检查您的提交消息是否符合 Conventional commit format

#### Commitlint 安装

参考 [官方安装文档](https://commitlint.js.org/#/?id=getting-started "官方安装文档")

```bash
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```
#### Commitlint 配置

根目录下创建 `commitlint.config.js` 配置文件，**注意：确保保存为 `UTF-8` 的编码格式**，否则可能会出现错误。

```bash
echo "export default { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
```

**增加配置项**

```javascript
module.exports = {
  // 继承的规则
  extends: ['@commitlint/config-conventional'],
  // @see: https://commitlint.js.org/#/reference-rules
  rules: {
    'subject-case': [0], // subject大小写不做校验
    // 类型枚举，git提交type必须是以下类型
    'type-enum': [
      // 当前验证的错误级别
      2,
      // 在什么情况下进行验证，always表示一直进行验证
      'always',
      [
        'feat', // 新增功能
        'fix', // 修复缺陷
        'docs', // 文档变更
        'style', // 代码格式（不影响功能，例如空格、分号等格式修正）
        'refactor', // 代码重构（不包括 bug 修复、功能新增）
        'perf', // 性能优化
        'test', // 添加疏漏测试或已有测试改动
        'build', // 构建流程、外部依赖变更（如升级 npm 包、修改 webpack 配置等）
        'ci', // 修改 CI 配置、脚本
        'revert', // 回滚 commit
        'chore' // 对构建过程或辅助工具和库的更改（不影响源文件、测试用例）
      ]
    ]
  }
}
```

**添加提交信息校验钩子**

执行下面命令生成 `commint-msg` 钩子用于 git 提交信息校验，husky9.x版本适配

```cs
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

husky9.x版本下此命令生成的`commint-msg` 配置（编码格式为UTF-16LE）：

git  commit -m "不规范的提交"。测试错误如下：

```bash
.husky/commit-msg: .husky/commit-msg: cannot execute binary file  
husky - commit-msg script failed (code 126)
```
解决办法：更改 `.husky` 目录下 pre-commit 文件的编码格式为  **`UTF-8` 的编码格式**

#### Commitlint 验证

正确的提交格式：`<type>(<scope>): <subject>` ，type 和 subject 默认必填

| 不规范的 commit msg<br><br>（ git commit -m "不规范的提交"），<br><br>提交失败 | 规范的 commit msg<br><br>（ git commit -m "feat:  规范的提交" ），<br><br>提交成功 |     |
| ------------------------------------------------------------- | ------------------------------------------------------------------- | --- |
|                                                               |                                                                     |     |
[参考博客](https://blog.csdn.net/weixin_51799004/article/details/138308098)

## 总结

通过结合使用 Commit Lint 和 Husky，你可以轻松规范 Git 提交信息，提高团队协作的效率。统一的提交信息不仅有助于代码的可读性，还有助于生成详细的变更日志，便于后续版本管理和发布。