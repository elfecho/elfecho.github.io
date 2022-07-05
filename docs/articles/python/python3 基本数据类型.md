## 基本数据类型

一共有六种：Number（数字），String（字符串)，List（列表），Tuple（元组），Set（集合），Dictionary（字典），接下来依次说明这六种类型。

- 不可变数据（3个）： Number(数字)、String(字符串)、Tuple(元组)
- 可变数据（3个）：List(列表)、Dictionary(字典)、Set(集合)


### 1. Number 数字类型

python中数字有四种类型：整数、布尔型、浮点数和复数。

-   **int** (整数), 如 1, 只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。
-   **bool** (布尔), 如 True。
-   **float** (浮点数), 如 1.23、3E-2
-   **complex** (复数), 如 1 + 2j、 1.1 + 2.2j

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

### 2. String 字符串

- Python中`单引号'`与`双引号"`使用完全相同。
- 使用三引号（如`'''XXX'''`或者`"""XXX"""`）可以指定一个多行字符串。
- 转义字符为`反斜杠 \`。
- 使用r可以让反斜杠不发生转义，如`r"this is a line with \n"`，会原样输出`\n`。
- 字符串拼接用+运算符。
- Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。
- 字符串的截取的语法格式如下：`变量[头下标:尾下标:步长]`。


```python
>>> str = 'Runoob'
>>> str[1]
u
>>> str[-1]
b
>>> str[1:3]
un
>>> str[0:-1]
Runoo
>>> str[-2:]
ob
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

此外需要注意python中的字符串是不能改变的，即不能向字符串的某个索引位置赋新值，但是两个字符串可以相加（用 +）生成第三个字符串，字符串也可以与数字相乘（用 `*`），生成一个把该字符串重复n遍的字符串，n是整数乘数。+ 是字符串连接元素运算符，* 是重复字符串运算符。


`步长：`

Python 列表截取可以接收第三个参数，参数作用是截取的步长，以下实例在索引 1 到索引 8 的位置并设置为步长为 2（间隔一个位置）来截取字符串：

```python
>>> str = 'hello world'
>>> str[0:8:1]
'hello wo'
>>> str[0:8:2]
'hlow'
```

如果第三个参数为负数表示逆向读取，以下实例用于翻转字符串：

```python
>>> str = 'hello world'
>>> str[8:0:-1]
'row olle'
>>> str[8:0:-2]
'rwol'
```

### 3. List列表

- List（列表） 是 Python 中使用最频繁的数据类型。
- 列表中元素的类型可以`不相同`，它支持数字，字符串甚至可以包含列表（所谓嵌套）。
- 列表是写在`方括号 []`之间、用`逗号`分隔开的元素列表。
- 和字符串一样，列表同样可以被索引和截取，列表被截取后返回一个包含所需元素的新列表。
- 列表截取的语法格式为：`变量[头下标:尾下标]`。索引值以 `0 为开始值， -1 为从末尾的开始位置`。
- 注：List列表可以类比成javascript中的索引数组。


```python
>>> list = ['abc', 123, 2.3, True, [1, 3]]
>>> type(list)
<type 'list'>
>>> tinylist = [456, 'hello']
>>> print(list) # 输出完整列表
>>> print(list[0]) # 输出列表第一个元素(返回元素)
>>> print(list[1,3]) # 从第二个开始输出到第三个元素(返回列表)
>>> print(list[2:]) # 输出从第三个元素开始的所有元素(返回列表)
>>> print(tinylist * 2) # 输出两次列表
>>> print(list + tinylist) # 连接列表
```

与python字符串的不同之处是，List中的元素是可以改变的。

在python列表切片的时候，也可以使用三个参数，第三个参数表示步长。如果第三个参数是负的，表示逆序切片。

```python
>>> letters = ['h', 'e', 'l', 'l', 'o']
>>> letters[1:4:2]
['e', 'l']
>>> letters[4:2:-1]
['o', 'l']
```

### 4. Tuple元组

-   元组（tuple）与列表类似，不同之处在于元组的元素`不能修改`。
-   元组写在`小括号 ()` 里，元素之间用逗号隔开。
-   元组中的元素类型也`可以不相同`

```python
>>> tuple = ('abc', 123, 2.33)
>>> type(tuple)
<type 'tuple'>
>>> tinytuple = [456, 'hello']
>>> print(list) # 输出完整元组
>>> print(list[0]) # 输出元组第一个元素(返回元素)
>>> print(list[1,3]) # 从第二个开始输出到第三个元素(返回元组)
>>> print(list[2:]) # 输出从第三个元素开始的所有元素(返回元组)
>>> print(tinytuple * 2) # 输出两次元组
>>> print(list + tinytuple) # 连接元组
```

注意：`构建0个或单个元素的元组是特殊的。`

```python
>>> tup1 = () # 空元祖
>>> tup2 = (10, ) # 一个元素时需要在元素后面加逗号，否则会转换为括号里面对应的类型
```

#### 序列小结

**序列**包含String(字符串)、Tuple(元组)、List(列表)

共同点：
- 序列的特定都是有顺序的
- 可以通过索引进行获取元素
- 通过`+`进行拼接
- 使用 `in` 进行判断元素是否在序列里面，使用 `not in` 判断元素是否不在序列中
- 通过 `len` 判断序列的长度
- 通过 `max` 判断序列里面的最大值、通过 `min` 判断序列里面的最小值

```python
>>> str = 'hello world'
>>> list = [1,2,3,4,5]
>>> 3 in list # True
>>> 'w' in str # True
>>> 'z' not in str # True
>>> len(str) # 11
>>> max(str) # w
>>> min(str) # ' '
```


![upgit_20220704_1656920664.png](https://raw.githubusercontent.com/elfecho/upgit-pic/master/2022/07/upgit_20220704_1656920664.png)




