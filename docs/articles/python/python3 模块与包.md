## 模块

如果你从 Python 解释器退出再进入，那么你定义的所有的方法和变量就都消失了。

为此 Python 提供了一个办法，把这些定义存放在文件中，为一些脚本或者交互式的解释器实例使用，这个文件被称为模块。

### 什么是模块

在 Python 中，⼀个 .py ⽂件就可以称之为 ⼀个模块（Module ）

**模块分类**

模块分为三种：
1. 内置标准模块
（⼜称标准库）执⾏ help(‘modules’) 查看所有 python ⾃带模块列表
2. 第三⽅开源模块
	可通过 pip install 模块名 联⽹安装
3. ⾃定义模块
模块导⼊ & 调⽤

导⼊模块有以下⼏种⽅式：
```python
import module_a #导⼊ 如果路径过长可以使用 `import module_a.xx.xx as m` 重命名
from module import xx # 导⼊某个模块下的某个⽅法 or ⼦模块
from module.xx.xx import xx as rename #导⼊后⼀个⽅法后重命名
from module.xx.xx import * #导⼊⼀个模块下的所有⽅法，不建议使⽤
module_a.xxx #调⽤
```

**注意：模块⼀旦被调⽤，即相当于执⾏了另外⼀个 py ⽂件⾥的代码**

导出模块可以通过 `__all__` 进行设置全局需要导出哪些变量或者方法

```python
# c1.py
__all__ = ['a', 'b']
a = 1
b = 2
c = 3
```

c2.py 引入 c1.py，当通过 `*` 导入模块时，`__all__`就可以起作用了

```python
# c2.py
from c1 import *  # 不建议使用 *  的方式进行引入
print(a)  # 1
print(b)  # 2
print(c)  # 报错
```

如果需要引入多个变量时，可以通过逗号分割

```python
from c1 import a, b, c
print(a)  # 1
print(b)  # 2
print(c)  # 3
```

python语言虽然没限制一行代码的字符，但是建议不要超过80个字符，如果需要引入多个变量的话，可以通过 `\` 进行换行,  或者通过 () 进行包裹

```python
from c1 import a, b,\
 c
from c1 import (a, b,
 c)
```

练习例子：[import 练习](https://gitee.com/elfeach/python-demo/tree/master/demo2/sub_test)

#### ⾃定义模块

test.py

```python
import my_module
my_module.sayhi("qinyun")
my_module.py
```

my_module.py

```python
print("my first module")
def sayhi(name):
    print("大家好")
```

结果

```
my first module
大家好
```

#### 模块的查找路径

有没有发现，⾃⼰写的模块只能在当前路径下的程序⾥才能导⼊，换⼀个⽬录再导⼊⾃⼰的模块就报错 说找不到了， 这是为什么？ 这与导⼊模块的查找路径有关模板的查找路径。

```python
import sys
print(sys.path)

['D:\\myProject\\python-demos\\demo2\\sub_test', 'D:\\install\\python\\python36.zip', 'D:\\install\\python\\DLLs', 'D:\\install\\python\\lib', 'D:\\install\\python', 'D:\\install\\python\\lib\\site-packages']
```

输出（注意不同的电脑可能输出的不太⼀样）
你导⼊⼀个模块时， Python 解释器会按照上⾯列表顺序去依次到每个⽬录下去匹配你要导⼊的模块名， 只要在⼀个⽬录下匹配到了该模块名，就⽴刻导⼊，不再继续往后找。
注意列表第⼀个元素为空，即代表当前⽬录，所以你⾃⼰定义的模块在当前⽬录会被优先导⼊。
我们⾃⼰创建的模块若想在任何地⽅都能调⽤，那就得确保你的模块⽂件⾄少在模块路径的查找列表 中。 我们⼀般把⾃⼰写的模块放在⼀个带有“site-packages” 字样的⽬录⾥，我们从⽹上下载安装的各种第三⽅的模块⼀般都放在这个⽬录。

#### 第3⽅开源模块安装

可以直接通过 pip 安装

```python
pip3 install paramiko #paramiko 是模块名pip命令会⾃动下载模块包并完成安装。
软件⼀般会被⾃动安装你 python 安装⽬录的这个⼦⽬录⾥
```

```python
/your_python_install_path/3.6/lib/python3.6/site-packages
```

pip 命令默认会连接在国外的 python 官⽅服务器下载，速度⽐较慢，你还可以使⽤国内的⾖瓣源，数据 会定期同步国外官⽹，速度快好多 -i 后⾯跟的是⾖瓣源地址 —trusted-host 得加上，是通过⽹站 https 安全验证⽤的

pip 命令默认会连接在国外的 python 官⽅服务器下载，速度⽐较慢，你还可以使⽤国内的⾖瓣源，数据 会定期同步国外官⽹，速度快好多 -i 后⾯跟的是⾖瓣源地址 —trusted-host 得加上，是通过⽹站 https 安全验证⽤的

```bash
pip install -i http://pypi.douban.com/simple/sayhi --trusted-host
pypi.douban.com #sayhi是模块名
```

使⽤
下载后，直接导⼊使⽤就可以，跟⾃带的模块调⽤⽅法⽆差


**vscode 小技巧：**
vscode如何隐藏python编译时生成的`__pycache__`文件呢？

可以通过设置里面进行搜索 `files.exclude` 然后添加 `**/__pycache__` 即可隐藏 `__pycache__`文件

### `__init__` 的用法

`__init__.py` 文件的作用是将文件夹变为一个Python模块,Python 中的每个模块的包中，都有`__init__.py` 文件。  

通常__init__.py 文件为空，但是我们还可以为它增加其他的功能。我们在导入一个包时，实际上是导入了它的__init__.py文件。这样我们可以在__init__.py文件中批量导入我们所需要的模块，而不再需要一个一个的导入。

```python
# package
# __init__.py
import re
import urllib
import sys
import os
# a.py
import package 
print(package.re, package.urllib, package.sys, package.os)
```

**注意这里访问__init__.py文件中的引用文件，需要加上包名。**

### 模块里的内置变量 

使用函数 `dir()` 可以返回模块里

1. `__file__`

所在模块：os
变量作用：指向当前文件
当前文件的完整路径：os.path.abspath(__file__)
当前文件所属目录：os.path.dirname(os.path.abspath(__file__))
当前文件所属目录的上级目录：os.path.dirname(os.path.dirname(os.path.abspath(__file__)))

```python
import os
print(__file__)
print(os.path.abspath("c8.py"))
print(os.path.abspath(__file__))
print(os.path.dirname(os.path.abspath(__file__)))
print(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

#运行：
D:\myProject\python-demos\demo2\sub_test\c8.py
D:\myProject\python-demos\demo2\sub_test\c8.py
D:\myProject\python-demos\demo2\sub_test
D:\myProject\python-demos\demo2
```

2. `__name__`

这是python内置的系统变量。

当python程序被执行时，入口文件即python解释器紧跟的那个py文件，在这个文件中__name__的值为__main__，在其它py文件中的__name__的值都等于所在文件的文件名(不包含.py后缀)。通常，我们使用`if __name__ == "__main__"`来判断当前文件是否是入口文件，以便判断是否要执行这个if语句中的代码块。

示例：

t/t1/c3.py

```python
'''
    This is a c3 doc
'''
print('name:\t' + __name__)
print('package\t' + (__package__ or '当前模块不属于任何包'))
print('doc\t' + (__doc__ or '当前模块暂无文档注释'))
print('file\t' + __file__)
```

c8.py

```python
import t.t1.c3

print('~~~~~~~~~~~~~~~~~~~~C8~~~~~~~~~~~~~~~~~~~~~~~~')
print('name:\t' + __name__)
print('package\t' + (__package__ or '当前模块不属于任何包'))
print('doc\t' + (__doc__ or '当前模块暂无文档注释'))
print('file\t' + __file__)
if __name__ == "__main__":
    print('this is main code')
```

结果

```python
>>> python c8.py

name:   t.t1.c3
package t.t1
doc
    This is a c3 doc

file    D:\myProject\python-demos\demo2\sub_test\t\t1\c3.py
~~~~~~~~~~~~~~~~~~~~C8~~~~~~~~~~~~~~~~~~~~~~~~
this is main code
name:   __main__
package 当前模块不属于任何包
doc     当前模块暂无文档注释
file    c8.py
```

从返回结果可以分析，
- `__name__` 是引入模块的包名与模块名结合,入口文件返回`__main__`
- `__package__`是引入模块的包名，入口文件的`__package__`是空
- `__doc__`是文档注释描述，无文档注释的话为空
- `__file__`是引入模块的绝对路径，入口文件的相对路径

## 什么是包

其实，一个包就是一个文件目录，你可以把同一个业务线的文件放在一起

![upgit_20220719_1658209070.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220719_1658209070.png)

## 包和模块常见的几个错误

- 包和模块是不会被重复导入的
- 避免循环引入模块
- 模块⼀旦被调⽤，即相当于执⾏了另外⼀个 py ⽂件⾥的代码


