[`pipenv`](https://github.com/pypa/pipenv)是[Python官方推荐](https://packaging.python.org/tutorials/managing-dependencies/#managing-dependencies)的包管理工具。可以说，它集成了`virtualenv`, `pip`和`pyenv`三者的功能。其目的旨在集合了所有的包管理工具的长处，如: `npm`, `yarn`, `composer`等的优点。

它能够自动为项目创建和管理虚拟环境，从`Pipfile`文件添加或删除安装的包，同时生成`Pipfile.lock`来锁定安装包的版本和依赖信息，避免构建错误。

`pipenv`主要解决了如下问题:

-   不用再单独使用`pip`和`virtualenv`, 现在它们合并在一起了
-   不用再维护`requirements.txt`, 使用`Pipfile`和`Pipfile.lock`来代替
-   可以使用多个python版本(`python2`和`python3`)
-   在安装了`pyenv`的条件下，可以自动安装需要的Python版本