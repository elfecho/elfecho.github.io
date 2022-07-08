## 条件控制

Python 条件语句是通过一条或多条语句的执行结果（True 或者 False）来决定执行的代码块。

可以通过下图来简单了解条件语句的执行过程:


![upgit_20220708_1657251627.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220708_1657251627.png)


Python 中用 **elif** 代替了 **else if**，所以if语句的关键字为：**if – elif – else**。

**注意：**
1. 每个条件后面要使用冒号 :，表示接下来是满足条件后要执行的语句块。
2. 使用缩进来划分语句块，相同缩进数的语句在一起组成一个语句块。
3. 在Python中没有switch – case语句



以下为if中常用的操作运算符:

| 操作符 | 描述                     |
| :----- | :----------------------- |
| `<`    | 小于                     |
| `<=`   | 小于或等于               |
| `>`    | 大于                     |
| `>=`   | 大于或等于               |
| `==`   | 等于，比较两个值是否相等 |
| `!=`   | 不等于                   |

示例：

```python
account = 'elfecho'
password = '123456'

user_account = input('Please input account:')
user_password = input('Please input password:')

if account == user_account and password == user_password:
    print('success')
else:
    print('error')

```


## 循环

Python 中的循环语句有 for 和 while。
Python 循环语句的控制结构图如下所示：



![upgit_20220708_1657260229.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220708_1657260229.png)




