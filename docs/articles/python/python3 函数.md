> 函数是组织好的，可重复使用的，用来实现单一，或相关联功能的代码段。

## 定义一个函数

你可以定义一个由自己想要功能的函数，以下是简单的规则：

-   函数代码块以 **def** 关键词开头，后接函数标识符名称和圆括号**()**。
-   任何传入参数和自变量必须放在圆括号中间。圆括号之间可以用于定义参数。
-   函数的第一行语句可以选择性地使用文档字符串—用于存放函数说明。
-   函数内容以冒号起始，并且缩进。
-   **return [表达式]** 结束函数，选择性地返回一个值给调用方。不带表达式的return相当于返回 None。

语法：

```python
def functionname( parameters ): 
	"函数_文档字符串" 
	function_suite 
	return [expression]
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

-   必备参数
-   关键字参数
-   默认参数
-   不定长参数

### 必备参数

必备参数须以正确的顺序传入函数。调用时的数量必须和声明时的一样。

调用 add() 函数，你必须传入一个参数，不然会出现语法错误：

```python
def add(x, y):
    result = x + y
    return result
    
a = add(1)
```

以上实例输出结果：

```python
Traceback (most recent call last):
  File "c5.py", line 5, in <module>
    a = add(1)
TypeError: add() missing 1 required positional argument: 'y'
```