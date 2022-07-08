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


## 循环语句

Python 中的循环语句有 for 和 while。
Python 循环语句的控制结构图如下所示：



![upgit_20220708_1657260229.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220708_1657260229.png)



### while 循环

Python 中 while 语句的一般形式：

```python
while 判断条件(condition)：
    执行语句(statements)……
```

同样需要注意冒号和缩进。另外，在 Python 中没有 do..while 循环。

以下实例使用了 while 来计算 1 到 100 的总和：

```python
#!/usr/bin/env python3
 
n = 100
 
sum = 0
counter = 1
while counter <= n:
    sum = sum + counter
    counter += 1
 
print("1 到 %d 之和为: %d" % (n,sum))
'''
执行结果如下：

1 到 100 之和为: 5050
'''
```

#### while 循环使用 else 语句

如果 while 后面的条件语句为 false 时，则执行 else 的语句块。

语法格式如下：

```python
while <expr>:
    <statement(s)>
else:
    <additional_statement(s)>
```

expr 条件语句为 true 则执行 statement(s) 语句块，如果为 false，则执行 additional_statement(s)。

循环输出数字，并判断大小：

```python
count = 0
while count < 5:
   print (count, " 小于 5")
   count = count + 1
else:
   print (count, " 大于或等于 5")
```

执行以上脚本，输出结果如下：

```python
0  小于 5
1  小于 5
2  小于 5
3  小于 5
4  小于 5
5  大于或等于 5
```

### for 语句

Python for 循环可以遍历任何可迭代对象，如一个列表或者一个字符串。

for循环的一般格式如下：

```python
for <variable> in <sequence>:
    <statements>
else:
    <statements>
```

以下 for 实例中使用了 break 语句，break 语句用于跳出当前循环体：

```python
a = [1, 2, 3]
for x in a:
    if x == 2:
        break  # 跳出整个for循环,后续else也不会被执行
    print(x)
else：
	print('EOF')
```

以下 for 实例中使用了 break 语句，continue 语句用于单次条件循环后继续循环体：

```python
a = [1, 2, 3]
for x in a:
    if x == 2:
        continue  # 跳出单个条件，继续循环
    print(x)
```

误区：

```python
a = [['apple', 'orange', 'banner', 'grape'], (1, 2, 3)]

for x in a:
    for y in x:
        if y == 'orange':
            break  # 这里只是跳出内部的循环，外部的循环还在循环
        print(y, end=' ')
else:
    print('fruit is gone')

'''
打印结果如下:
apple 1 2 3 fruit is gone
'''
```

### range函数

如果需要实现传统语音的遍历数字序列的方式，可以使用内置range()函数。它会生成数列，例如:

```python
for i in range(5):
	print(i)
'''
0
1
2
3
4
'''
```

使用range指定区间的值：

```python
for i in range(5, 9):
	print(i)
'''
5
6
7
8
'''
```

使range以指定数字开始并指定不同的增量(甚至可以是负数，有时这也叫做'步长'):

```python
for x in range(0, 10, 2):
    print(x, end=" | ")
'''
0 | 2 | 4 | 6 | 8
'''
for x in range(10, 0, -2):
    print(x, end=" | ")
'''
10 | 8 | 6 | 4 | 2
'''
```

结合range()和len()函数以遍历一个序列的索引,如下所示:

```python
a = ['Google', 'Baidu', 'Runoob', 'Taobao', 'QQ']
for i in range(len(a)):
    print(i, a[i])
'''
0 Google
1 Baidu
2 Runoob
3 Taobao
4 QQ
'''
```

也可以使用range()函数创建一个列表

```python
list(range(5))
# [0, 1, 2, 3, 4]
```

练习题

```python
a = [1, 2, 3, 4, 5, 6, 7, 8] # 如何实现打印出 1, 3, 5, 7


for i in range(0, len(a), 2):
    print(a[i], end=' | ')
```