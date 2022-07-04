## 基本数据类型

一共有六种：Number（数字），String（字符串，List（列表），Tuple（元组），Set（集合），Dictionary（字典），接下来依次说明这六种类型。

- 不可变数据（3个）： Number(数字)、String(字符串)、Tuple(元组)
- 可变数据（3个）：List(列表)、Dictionary(字典)、Set(集合)

### number 数字类型

python3支持int，float，bool，complex等几种数字类型

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

2进制、8进制、10进制、16进制
```python
>>> 0b10 # 二进制
2
>>> 0o10 # 八进制
8
```













![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)

