## 基本数据类型

一共有六种：Number（数字），String（字符串)，List（列表），Tuple（元组），Set（集合），Dictionary（字典），接下来依次说明这六种类型。

- 不可变数据（3个）： Number(数字)、String(字符串)、Tuple(元组)
- 可变数据（3个）：List(列表)、Dictionary(字典)、Set(集合)

### Number 数字类型

python3支持int(整型)，float(浮点)，bool(布尔)，complex(复数)等几种数字类型

内置函数type和isinstance都可以用来判断某个变量是否是给定的某个类型。

用法是  typyie("变量名"，"类型名")，如果变量属于给定类型返回true，否则返回false。

不同之处在于instance会认为子类也是一种父类类型，而type认为子类不是父类类型。

```python
>>> type(10)
<type 'int'>
>>> type(1.1)
<type 'float'>
>>> type(1+1)
<type 'int'>
>>> type(1/1)
<type 'float'>
>>> type(1//1)
<type 'int'>
>>> type(1//2)
<type 'int'>
```

在python3中，bool是int的子类，True就是1，False就是0，True和False可以直接参加int类型数字的运算

```python
>>> issubclass(bool, int)
True
>>> True == 1
True
>>> False == 0
True
>>> True + 1
2
>>> False + 1
1
>>> 1 is True
False
>>> o is True
False
<type 'int'>
```

1. Python可以同时为多个变量赋值，如a,b=1,2。
2. 一个变量可以通过赋值指向不同类型的对象。
3. 数值的除法包含两个运算符：/返回一个浮点数，/返回一个整数。
4. 在混合计算时，Python会把整型转换成为浮点数。

另外需要注意的一点是python中的赋值语句相当于是创建一个原变量的引用，而非拷贝。

#### 2进制、8进制、10进制、16进制

```python
>>> 0b11 # 二进制
3
>>> 0o11 # 八进制
9
>>> 0x1F # 十六进制
31
>>> 11 # 十进制
11
```

其他进制转2进制使用 `bin('变量')`
其他进制转10进制使用 `int('变量')`
其他进制转16进制使用 `hex('变量')`
其他进制转8进制使用 `oct('变量')`

### String 字符串






![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)


![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)

