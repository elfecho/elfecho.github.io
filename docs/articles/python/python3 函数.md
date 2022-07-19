> 函数是组织好的，可重复使用的，用来实现单一，或相关联功能的代码段。

## 定义一个函数

你可以定义一个由自己想要功能的函数，以下是简单的规则：

-   函数代码块以 **def** 关键词开头，后接函数标识符名称和圆括号 **()**。
-   任何传入参数和自变量必须放在圆括号中间，圆括号之间可以用于定义参数。
-   函数的第一行语句可以选择性地使用文档字符串—用于存放函数说明。
-   函数内容以冒号 : 起始，并且缩进。
-   **return [表达式]** 结束函数，选择性地返回一个值给调用方，不带表达式的 return 相当于返回 None。

语法：

```python
def 函数名（参数列表）:
    函数体
```

## 函数调用

定义一个函数只给了函数一个名称，指定了函数里包含的参数，和代码块结构。
这个函数的基本结构完成以后，你可以通过另一个函数调用执行，也可以直接从Python提示符执行。

如下实例调用了 add() 函数：
```python
def add(x, y):
    result = x + y
    return result

result = add(2, 3)
print(result)

# 运行
5
```

当函数返回多个结果

```python
def damage(skill1, skill2):
    damage1 = skill1 * 3
    damage2 = skill2 * 2 + 10
    return damage1, damage2
  
damages = damage(2, 3)
print(damages） # （6, 16）
print(damages[0], damages[1])  # 6 16 这种方式获取，不利于维护以及查看
print(type(damages))  # <class 'tuple'>

# 使用序列解包的方式接收返回的值，可读性强，便于维护
skill1_damage, skill2_damage = damage(2, 3)
print(skill1_damage, skill2_damage) # 6 16
```

### 序列解包与链式赋值

序列解包的个数一定要相等

```python
d = 1, 2, 3
a, b, c = d
print(a, b, c)
e = f = g = 1
print(e, f, g)
# 运行
1 2 3
1 1 1
```

## 参数

以下是调用函数时可使用的正式参数类型：

-  必需参数
-  关键字参数
-   默认参数
-   可变参数

### 必需参数

必需参数须以正确的顺序传入函数。调用时的数量必须和声明时的一样。

调用 add() 函数，你必须传入一个参数，不然会出现语法错误：

```python
def add(x, y):
	# x, y 形式参数 形参
    result = x + y
    return result
    
a = add(1) # 实际参数 实参
```

以上实例输出结果：

```python
Traceback (most recent call last):
  File "c5.py", line 5, in <module>
    a = add(1)
TypeError: add() missing 1 required positional argument: 'y'
```

### 关键字参数

关键字参数和函数调用关系紧密，函数调用使用关键字参数来确定传入的参数值。

使用关键字参数允许函数调用时参数的顺序与声明时不一致，因为 Python 解释器能够用参数名匹配参数值。

以下实例在函数 add() 调用时使用参数名：

```python
def add(x, y):
    result = x + y
    return result
    
c = add(y=3, x=2)  # 代码可读性增强
```

### 默认参数

调用函数时，默认参数的值如果没有传入，则被认为是默认值。下例会打印默认的age，如果age没有被传入：

```python
def printinfo(name, age=35):
    print('name is ' + name)
    print('age is ' + str(age))
    return

# 调用printinfo函数
printinfo(age=50, name="miki")
printinfo(name="miki")
```

```python
name is miki
age is 50
name is miki
age is 35
```

注意： 默认参数需要放在参数的最后面

### 可变参数

你可能需要一个函数能处理比当初声明时更多的参数。这些参数叫做不定长参数，和上述2种参数不同，声明时不会命名。基本语法如下：

```python
def functionname([formal_args,] *var_args_tuple ):
	function_suite 
	return [expression]
```

示例一：

```python
def printinfo(arg1, *vartuple):
    "打印任何传入的参数"
    print("输出: ")
    print(arg1)
    print(vartuple)


# printinfo 函数
printinfo(70, 60, 50)

'''
输出: 
70
(60, 50)
'''
```

示例二：

```python
def demo(*params):
    print(params)
    print(type(params))

a = (1, 2, 3, 4, 5)

demo(*a)

# *a的作用就是把元组a平铺出来

'''
(1, 2, 3, 4, 5)
<class 'tuple'>
'''
```

加了两个星号 ** 的参数会以字典的形式导入

```python
def printinfo(arg1, **vardict):
    print("输出: ")
    print(arg1)
    print(vardict)
    for key, value in vardict.items():
		print(key, ':', value)
    


# 调用printinfo 函数
printinfo(1, a=2, b=3)
dic = {'a': 2, 'b': 3}
printinfo(1, **dic)   # 结果是一样的

'''
输出: 
1
{'a': 2, 'b': 3}
a : 2
b : 3
'''
```

## 匿名函数

Python 使用 lambda 来创建匿名函数。

所谓匿名，意即不再使用 **def** 语句这样标准的形式定义一个函数。

-   lambda 只是一个表达式，函数体比 **def** 简单很多。
-   lambda 的主体是一个表达式，而不是一个代码块。仅仅能在 lambda 表达式中封装有限的逻辑进去。
-   lambda 函数拥有自己的命名空间，且不能访问自己参数列表之外或全局命名空间里的参数。
-   虽然 lambda 函数看起来只能写一行，却不等同于 C 或 C++ 的内联函数，后者的目的是调用小函数时不占用栈内存从而增加运行效率。

### 语法

lambda 函数的语法只包含一个语句，如下：

```python
lambda [arg1 [,arg2,.....argn]]:expression
```

设置参数 a 加上 10:

## 实例

```python
x = lambda a : a + 10 print(x(5))
# 结果： 15
```

以下实例匿名函数设置两个参数：

```python
sum = lambda arg1, arg2: arg1 + arg2

# 调用sum函数
print ("相加后的值为 : ", sum( 10, 20 ))
print ("相加后的值为 : ", sum( 20, 20 ))
```

以上实例输出结果：

```python
相加后的值为 :  30
相加后的值为 :  40
```

