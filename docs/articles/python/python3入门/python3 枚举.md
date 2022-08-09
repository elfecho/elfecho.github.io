在Python3中新增加了一种数据类型—[枚举](https://so.csdn.net/so/search?q=%E6%9E%9A%E4%B8%BE&spm=1001.2101.3001.7020)。

### 枚举的定义

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

### 枚举和类

本质上，枚举是一种特殊的类，但是它和普通的类有很多的不同。

1. 定义枚举时，其枚举成员的名称不允许相同，而在普通类定义中，其类成员的成员是允许相同的

```python
class VIP(Enum):
    YELLOW = 1
    YELLOW = 2
    BLACK = 3
    RED = 4
# 运行会报错
```
2. 默认情况下，不同的成员值允许相同。但是两个相同值的成员，其第二个成员名称是第一个成员名称的别名；因此在访问枚举成员时，只能获取第一个成员。

```python
class VIP(Enum):
    YELLOW = 1
    GREEN = 1  # 这个相当于第一个的别名
    BLACK = 3
    RED = 4
print(VIP.GREEN)  # 打印出 VIP.YELLOW
```

3. 若要限制枚举中成员值都各不相同，则需要使用装饰器unique

继承 IntEnum 时，枚举成员值必须为数字类型
```python
from enum import IntEnum, unique

@unique
class VIP(IntEnum):
    GREEN = 1
    YELLOW = 1
    BLACK = 3
    RED = 4

print(VIP(1))
'''
运行结果会报错
'''
```


### 枚举成员的访问

方法一：
```python
from enum import Enum

class VIP(Enum):
    GREEN = 1
    YELLOW = 2
    BLACK = 3
    RED = 4


print(VIP.RED)
print(VIP.RED.value)
print(VIP.RED.name)
print(type(VIP.RED))
print(type(VIP.RED.name))
'''
运行结果
VIP.RED
4
RED
<enum 'VIP'>
<class 'str'>
'''
```

通过Color.RED并不能访问其名称或数值，只能得到枚举成员

方法二(不推荐)：

```python
class VIP(Enum):
    GREEN = 1
    YELLOW = 2
    BLACK = 3
    RED = 4

print(VIP(1))
print(VIP['GREEN'])
print(VIP(1) == VIP['GREEN'])
'''
运行结果
VIP.GREEN
VIP.GREEN
True
'''
```

通过 VIP(1)和 VIP['GREEN'] 可以获取枚举成员。当我们通过数值来判断枚举成员时，使用VIP(1)这样的方式非常的简单，如 VIP(1) == VIP.GREEN

方法三:

```python
class VIP(Enum):
    GREEN = 1
    GREEN_ALIAS = 1
    YELLOW = 2
    BLACK = 3
    RED = 4


for i in VIP:
    print(i)

for i in VIP.__members__.items():
    print(i)

for i in VIP.__members__:
    print(i)

'''
运行结果
VIP.GREEN
VIP.YELLOW
VIP.BLACK
VIP.RED
('GREEN', <VIP.GREEN: 1>)
('GREEN_ALIAS', <VIP.GREEN: 1>)
('YELLOW', <VIP.YELLOW: 2>)
('BLACK', <VIP.BLACK: 3>)
('RED', <VIP.RED: 4>)
VIP.GREEN
VIP.GREEN_ALIAS
VIP.YELLOW
VIP.BLACK
VIP.RED
'''
```

使用`for`循环可以遍历枚举成员。当使用`Color.__members__.items()`时，可以将枚举成员中相同值的成员也打印出来


### 枚举的比较

枚举成员之间的比较只能使用比较运算符 `==` 和身份运算符 `is`

```python
class VIP(Enum):
    GREEN = 1
    YELLOW = 2
    BLACK = 3
    RED = 4

print(VIP(1))
if VIP(1) == VIP.GREEN:
    print('GREEN == 1')

if VIP(2) is VIP.YELLOW:
    print('YELLOW == 2')
'''
运行结果
VIP.GREEN
GREEN == 1
YELLOW == 2
'''
```

枚举成员只能和枚举成员之间比较，而不能和枚举成员的数值进行比较