## 快速上传图片到github并插入图片地址到Obsidian

Obsidian已经有一个[Image auto upload](https://github.com/renmu123/obsidian-image-auto-upload-plugin)外挂，使用 [PicGo](https://github.com/Molunerfinn/PicGo)在背景执行是，将贴入Obsidian的图片自动上传到Github存储库里，想要节省操作步骤的朋友也可试用看看，但个人觉得PicGo必须在执行状态才能上传，整合性不如upgit方便

### Github Token 设定

1. 登录自己的Github->右上角头像->【Setting】->左侧拉到最底部->【Developer settings】→Personal access tokens→Generate new token
2. 输入【Note】、【Expiration】(有效期限)，在【Select scope】勾选repo以取得存储库读写权限
3. 在最下方点击【Generate token】

![upgit_20220725_1658733834.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658733834.png)

复制个人存取token：ghp_开头全部字串

![upgit_20220725_1658733907.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220725_1658733907.png)

### 建立专用的GitHub repo

【New repository】建立Public存储库

設定檔config.toml和upgit在同一個資料夾，依據自己GitHub的設定修改內容，範例如下：

