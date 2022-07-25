## 快速上传图片到github并插入图片地址到Obsidian

Obsidian已经有一个[Image auto upload](https://github.com/renmu123/obsidian-image-auto-upload-plugin)外挂，使用 [PicGo](https://github.com/Molunerfinn/PicGo)在背景执行是，将贴入Obsidian的图片自动上传到Github存储库里，想要节省操作步骤的朋友也可试用看看，但个人觉得PicGo必须在执行状态才能上传，整合性不如upgit方便

最后会介绍使用AutoHotkey的简单合用方法，同時介绍使用Obsidian Shell Commands外挂自动上传图片并插入GitHub图片地址的操作步骤。

### Github Token 设定

1. 登录自己的Github->右上角头像->【Setting】->左侧拉到最底部->【Developer settings】→Personal access tokens→Generate new token
2. 输入【Note】、【Expiration】(有效期限)，在【Select scope】勾选repo以取得存储库读写权限
3. 在最下方点击【Generate token】

![upgit_20220725_1658733834.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658733834.png)

复制个人存取token：ghp_开头全部字串

![upgit_20220725_1658733907.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658733907.png)


### 建立专用的GitHub repo

【New repository】建立Public存储库

### upgit 安装

[upgit下载链接](https://github.com/pluveto/upgit/releases)

![upgit_20220725_1658734559.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658734559.png)

下载后的档名改为 upgit.exe，然后设置Path环境变量指定到这个文件夹，然后新建一个config.toml作为upgit的设定档

语法

```
upgit \[--target-dir TARGET-DIR] \[--verbose] \[--size-limit SIZE-LIMIT] \[--wait] \[--clean] \[--raw] \[--no-log] \[--output-type OUTPUT-TYPE] \[--output-format OUTPUT-FORMAT] FILE \[FILE ...]
```

示例：

```bash
upgit demo.png -f markdown
upgit demo1.png -f markdown-simple
```

将demo.png图片上传到github，-f 后面是返回的格式

### upgit config.toml 设定

设定档config.toml和upgit在同一個资料夹，依據自己GitHub的设定修改內容，示例如下：

```
# =============================================================================
# UPGIT 配置
# =============================================================================

# 默认上传器
default_uploader = "github"

# 上传文件名的格式模板（仅特定上传器适配）
#   / 目录分隔符, 作用: 是区分目录
#   {year} 年份, 例如: 2022
#   {month} 月份, 例如: 02
#   {day} 天, 例如: 01
#   {unix_ts} 时间戳, 例如: 1643617626
#   {fname} 原始文件名，如 logo (不含后缀名)
#   {fname_hash} {fname}的 MD5 散列值
#   {ext} 文件后缀名, 例如.png
#   下面的例子生成的文件名预览: 2022/01/upgit_20220131_1643617626.png
#   如果目录不存在将会被程序自动创建
rename = "{year}/{month}/upgit_{year}{month}{day}_{unix_ts}{ext}"


# -----------------------------------------------------------------------------
# 自定义输出格式
# -----------------------------------------------------------------------------
#   {url} 图片文件的网络URL地址
[output_formats]
"bbcode" = "[img]{url}[/img]"
"html" = '<img src="{url}" />'
"markdown-simple" = "![01|700]({url})"

# -----------------------------------------------------------------------------
# 直链替换规则 RawUrl -[replace]-> Url
# -----------------------------------------------------------------------------

# 如果您的网络访问Github异常或者缓慢，您可以尝试下面的配置以开启CDN加速
# [replacements]
# "raw.githubusercontent.com" = "cdn.jsdelivr.net/gh"
# "/master" = "@master"

# =============================================================================
# 以下为各个上传器的配置示例. 用不到的留空即可
# =============================================================================

# Github 上传器
[uploaders.github]
# 保存文件的分支，例如 master 或 main
branch = "master"

# 您的拥有"repo"权限的 Github 令牌
# 获取Github Token连接: https://github.com/settings/tokens
pat = "ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# 您的公共Github存储库的名称
# 注意: 为了让您和他人可以访问到图片资源, 您的Github仓库一定要是公开的, 
#       在私有仓库中Github会拦截未授权的请求,你将会得到一个404.
repo = "upgit-pic"

# 您的 Github 用户名 
username = "elfecho"
```

### AutoHotkey整合

按〔Alt+V〕將剪贴板內的图片上传到GitHub，并將GitHub图片网址再插入剪贴板。

```bash
!v::
  ;;Run d:\util\upgit.exe :clipboard -o clipboard -f markdown
  Run d:\util\upgit.exe :clipboard --output-type clipboard --output-format markdown
  return
```

### 使用Obsidian Shell Commands外挂

1. 安装并启用Shell Commands外挂
2. 新增Shell命令
   - 新的命令命名为upgit
   - 命令内容：`upgit :clipboard -f markdown`将系统剪贴板内容上传到GitHub，并将执行结果以markdown格式输出到标准输出(stdout)

![upgit_20220725_1658735375.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658735375.png)

3. Output选用输出到目前档案的游标位置，亦即将GitHub的图片地址以Markdown格式插入目前游标位置

![upgit_20220725_1658735587.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658735587.png)

4. 将`Shell commands: Exexcute upgit` 设置快捷键以方便快速操作