### 1. 如何创建分支

```bash
git checkout -b develop
```

### 2.如何将开发好的分支代码合并到 master 主分支上去

先在使用的分支进行提交代码

```bash
git add .
git commit -m '提交的文字描述'
git pull origin master
git push -u origin develop
```

此时需要将分支切换到master分支上去

```bash
git checkout master
```

在合并代码前需要先拉取一下

```bash
git pull
```

拉取完成之后，就可以进行代码合并了

```bash
git merge develop
```

将develop分支合并到master完成后，再将master推送到远程分支上

```bash
git push -u origin master
```

**合并过程中Git的报错**

#### 解决Git中fatal: refusing to merge unrelated histories

```bash
$ git checkout master
switched to brach 'master'

$ git merge develop
fatal: refusing to merge unrelated histories
```

可能会在git pull或者git push中都有可能会遇到，这是因为两个分支没有取得关系。那么怎么解决呢？

`解决方案：`

在操作命令后面加--allow-unrelated-histories
例如：

```bash
git merge master --allow-unrelated-histories
```

如果是git pull或者git push报fatal: refusing to merge unrelated histories

同理：
```bash
git pull origin master --allow-unrelated-histories
```

### 3. 如何进行代码回滚

首先通过命令: `git log` 查看所有的提交记录

回退命令：

```bash
git reset --hard HEAD^ # 回退到上个版本
git reset --hard HEAD~3 # 回退到前3次提交之前，以此类推，回退到n次提交之前
git reset --hard commit_id # 退到/进到 指定commit的sha码
```

然后强推到远程：

```bash
git push origin HEAD --force
```

### 4. 为git仓库配置SSH公钥

#### 生成新的SSH密钥

```shell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

> 注意
> 如果你使用的是不支持 Ed25519 算法的旧系统，请使用以下命令：

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

#### 测试SSH密钥

```shell
ssh -T git@github.com
# Attempts to ssh to GitHub
```

#### 为设备设置多个SSH密钥

在 `.ssh` 目录下新建一个config文件夹，配置类似如下

```
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa

  
# coding.net
Host elfecho.coding.net
HostName coding.net
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_ed25519
```


