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

导出模块可以通过 `__all__` 进行设置需要导出哪些变量或者方法

```python
__all__ = ['a', 'b']
a = 1
b = 2
c = 3
```

练习例子：[import 练习](https://gitee.com/elfeach/python-demo/tree/master/demo2/sub_test)

小技巧：
vscode如何隐藏python编译时生成的`__pycache__`文件呢？

可以通过设置里面进行搜索 `files.exclude` 然后添加 `**/__pycache__` 即可隐藏 `__pycache__`文件