[`pipenv`](https://github.com/pypa/pipenv)是[Python官方推荐](https://packaging.python.org/tutorials/managing-dependencies/#managing-dependencies)的包管理工具。可以说，它集成了`virtualenv`, `pip`和`pyenv`三者的功能。其目的旨在集合了所有的包管理工具的长处，如: `npm`, `yarn`, `composer`等的优点。

它能够自动为项目创建和管理虚拟环境，从`Pipfile`文件添加或删除安装的包，同时生成`Pipfile.lock`来锁定安装包的版本和依赖信息，避免构建错误。

`pipenv`主要解决了如下问题:

-   不用再单独使用`pip`和`virtualenv`, 现在它们合并在一起了
-   不用再维护`requirements.txt`, 使用`Pipfile`和`Pipfile.lock`来代替
-   可以使用多个python版本(`python2`和`python3`)
-   在安装了`pyenv`的条件下，可以自动安装需要的Python版本


## 安装

为了方便使用, 建议**全局**安装

```
$ pip install pipenv
```

## 基本概念

-   虚拟环境如果不存在的话，会自动创建
-   当`install`命令没有传递参数指定安装包时，所有`[packages]`里指定的包都会被安装
-   `pipenv --three`可以初始化一个`python3`版本的虚拟环境
-   `pipenv --two`可以初始化一个`python2`版本的虚拟环境

## 添加shell补齐

如果使用的是`bash`, 可添加下面语句到`.bashrc`或`.bash_profile`

## 常用命令介绍

```bash
# 安装包
$ pipenv install

# 激活当前项目的虚拟环境
$ pipenv shell

# 安装开发依赖包
$ pipenv install pytest --dev

# 图形显示包依赖关系
$ pipenv graph

# 生成lockfile
$ pipenv lock

# 删除所有的安装包
$ pipenv uninstall --all
```

## 高级技巧

### 导入`requirements.txt`

当在执行`pipenv install`命令的时候，如果有一个`requirements.txt`文件，那么会自动从`requirements.txt`文件导入安装包信息并创建一个`Pipfile`文件。

同样可以使用`$ pipenv install -r path/to/requirements.txt`来导入`requirements.txt`文件

**注意**:

默认情况下，我们都是在`requirements.txt`文件里指定了安装包的版本信息的，在导入`requirements.txt`文件时，版本信息也会被自动写`Pipfile`文件里， 但是这个信息我们一般不需要保存在`Pipfile`文件里，需要手动更新`Pipfile`来删除版本信息

### 指定安装包的版本信息

为了安装指定版本的包信息，可以使用:

```bash
$ pipenv install requests==2.13.0
```

这个命令也会自动更新`Pipfile`文件

### 指定Python的版本信息

在创建虚拟环境的时候，我们可以指定使用的python版本信息，类似`pyenv`

```fallback
$ pipenv --python 3
$ pipenv --python 3.6
$ pipenv --python 2.7.14
```

`pipenv`会自动扫描系统寻找合适的版本信息，如果找不到的话，同时又安装了`pyenv`, 它会自动调用`pyenv`下载对应的版本的python

### 指定安装包的源[](https://crazygit.wiseturtles.com/2018/01/08/pipenv-tour/#%E6%8C%87%E5%AE%9A%E5%AE%89%E8%A3%85%E5%8C%85%E7%9A%84%E6%BA%90)

如果我们需要在安装包时，从一个源下载一个安装包，然后从另一个源下载另一个安装包，我们可以通过下面的方式配置

```fallback
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[[source]]
url = "http://pypi.home.kennethreitz.org/simple"
verify_ssl = false
name = "home"

[dev-packages]

[packages]
requests = {version="*", index="home"}
maya = {version="*", index="pypi"}
records = "*"
```

如上设置了两个源：

-   `pypi`([https://pypi.python.org/simple](https://pypi.python.org/simple))
-   `home`([http://pypi.home.kennethreitz.org/simple](http://pypi.home.kennethreitz.org/simple))

同时指定`requests`包从`home`源下载，`maya`包从`pypi`源下载

### 生成requirements.txt文件

我们也可以从`Pipfile`和`Pipfile.lock`文件来生成`requirements.txt`

```fallback
# 生成requirements.txt文件
$ pipenv lock -r

# 生成dev-packages的requirements.txt文件
# pipenv lock -r -d
```