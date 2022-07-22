在Python3中新增加了一种数据类型—[枚举](https://so.csdn.net/so/search?q=%E6%9E%9A%E4%B8%BE&spm=1001.2101.3001.7020)。

枚举的定义：
1. 导入enum模块；
2. 使用class定义枚举，并继承Enum类；
3. 定义枚举成员。


```python
from enum import Enum


class VIP(Enum):
    YELLOW = 1
    GREEN = 2
    BLACK = 3
    RED = 4

print(VIP.RED) # YIP.RED
```

上述代码中，定义了枚举VIP，其中VIP表示数据类型，而不是枚举的对象；RED = 0、BLUE = 1等称为枚举成员；RED表示成员名称(因为是常量，因此大写)；0表示成员值。