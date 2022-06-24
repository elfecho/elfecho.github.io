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