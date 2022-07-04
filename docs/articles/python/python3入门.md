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

python中字符串用 ‘ ’ 或者 “ ” 括起来，使用 \ 转义特殊字符。python中没有单独的字符类型，一个字符就是一个长度为1的字符串。

        python中字符串有两种索引方式，即正序索引与逆序索引，正序索引从0开始，逆序索引从-1开始。

![](https://img-blog.csdnimg.cn/20210730214451878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Noa19wbHVzcGx1cw==,size_1,color_000000,t_0)

截取字符串用 变量[头索引:尾索引]，其中包含投索引指向的字符，不包含尾索引指向的字符，即`前闭后开`区间。

```python
>>> str = 'Runoob'
>>> str[1]
u
>>> str[-1]
b
>>> str[1:3]
un
>>> str[-3:-1]
oo
```

        \ 可以用来使字符转义，使用r可以让反斜杠失效，即不发生转义。

```python
>>> print('Ob\nsidian')
Ob
sidian
>>> print(r'Ob\nsidian')
Ob\nsidian
>>> print('Ob\\nsidian')
Ob\nsidian
```

```python
>>> \n #换行
>>> \' #单引号
>>> \t #横向制表符
>>> \r #回车
```


此外 \ 还可以作为续行符使用，表明下一行语句与本行是连接着的，但是要注意本行中 \ 后边不能有任何字符。也可以用 '''   ''' 三引号作为跨行的换行符。

```python
>>> print('a\
... b')
a
b
>>> print('''a
... b''')
a
b
```

此外需要注意python中的字符串是不能改变的，即不能向字符串的某个索引位置赋新值，但是两个字符串可以相加（用 +）生成第三个字符串，字符串也可以与数字相乘（用 *），生成一个把该字符串重复n遍的字符串，n是整数乘数。+ 是字符串连接元素运算符，* 是重复字符串运算符。




![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)




