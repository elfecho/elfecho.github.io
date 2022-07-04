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
>>> type(True)
<type 'bool'>
>>> init(True)
1
>>> b(True)
1
```

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










![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)

