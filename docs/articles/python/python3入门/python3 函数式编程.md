## 闭包

在Python3中，没有函数重载。即当函数名相同，参数列表不同时，后面定义的函数会覆盖前面定义的函数。

```python
def add(x,y):
    return x+y

def add(x,y,z):
    return x+y+z

print(add(1,2,3)) # 6
print(add(1,2))  # 报错
```

一切皆对象, 即函数、类都是对象。


闭包 = 函数+环境变量(函数定义的时候)

```python
def curve_pre():
    a = 25
    def curve(x):
        return a * x * x
    return curve


a = 10
f = curve_pre()


print(f.__closure__)
print(f.__closure__[0].cell_contents)  # 查看闭包里面的变量,因为只有一个，所以取下标为0
print(f(2))
'''
运行结果
(<cell at 0x000001B866C58558: int object at 0x00000000562CC9B0>,)
25
100
'''
```

### 函数作为参数

在Python3中，函数也是对象，那么函数也可以作为其他函数的参数或返回值

```python
def square(x):
    return x * x

l = [1,2,3,4,5]

def fun(l, f):
    n = 0
    for m in l:
        l[n] = f(m)
        n += 1

fun(l, square)
print(l)
'''
运行结果
[1, 4, 9, 16, 25]
'''
```

### 函数作为返回值

```python
def fun():
    def add(x, y):
        return x + y
    return add

f1 = fun()
print(f1)
print(f1(1, 2))
f2 = fun()
print(f2)
print(f2(3, 5))
'''
运行结果
<function fun.<locals>.add at 0x000001BDA4F7CB70>
3
<function fun.<locals>.add at 0x000001BDA4F7CBF8>
8
'''
```

函数fun称为封闭函数；函数add称为嵌套函数。当每执行一次封闭函数，都会创建一个嵌套函数的新实例

### 嵌套函数

嵌套函数的作用：函数封装；闭包

```python
def fun():
    def add(x, y):
        return x + y
    return add


def add(a, b, c):
    return a + b + c

f = fun()
print(f(1, 2))
print(add(1, 2, 3))
'''
运行结果
3
6
'''
```

由于嵌套函数被封闭函数保护起来，并不在全局作用域中，因此可以重新定义一个和嵌套函数同名的函数。

嵌套函数本质上属于封闭函数的局部对象，因此并不能在外部直接访问

### 闭包

在讲闭包之前，先看下面的代码

```python
m = 1
def add(a):
    m = m + a
    return m

print(add(2))
print(m)
'''
运行结果
Traceback (most recent call last):
  File "c2.3.py", line 6, in <module>
    print(add(2))
  File "c2.3.py", line 3, in add
    m = m + a
UnboundLocalError: local variable 'm' referenced before assignment 
'''
```

m是全局变量，为什么在add函数中，m定义为局部变量？

当执行赋值运算符时，若左边的变量存在，则表示赋值操作；若左边的变量不存在，则表示创建新变量并赋值。在函数中，变量会优先检索局部变量，再去检索全局变量。

在add函数中，执行m + a操作时，会先检索局部变量m不存在，再去检索全局变量m，因此这里的m为全局变量。当执行m = 操作时，检索局部变量m不存在，会直接创建新的局部变量m，因此这里的m为局部变量。左边的m为局部变量，右边的m为全局变量，产生冲突运行失败。

解决方法1：

```python
m = 1
def add(a):
    n = m + a
    return n

print(add(2))
print(add(3))
print(m)
'''
运行结果
3
4
1
'''
```

解决方法2：

```python
m = 1
def add(a):
	global m
    m = m + a
    return m

print('m = %d' % m)
print(add(2))
print('m = %d' % m)
print(add(3))
print('m = %d' % m)
'''
运行结果
m = 1
2
m = 3
3
m = 6
'''
```

使用global关键字，在add函数内声明m为全局变量。由于使用了全局变量，因此每次运行的结果都会保存在全局变量m中。

**问题**：当一个函数每次运行都要保存执行结果，并用作下一次函数运行。该如何实现？

**方法1**：使用全局变量，函数内使用global关键字声明。函数每次运行都会修改全局变量，并且下一次函数运行时全局变量为修改后的数值

**方法2**： 闭包

```python
step = 1

def fun(a):
    def add(b):
        nonlocal a
        a = a + b
        return a
    return add


f = fun(step)

print(f(2))
print(f(3))

print('step = %d' % step)
'''
运行结果
3
6
step = 1
'''
```

闭包：

1. 必须包含一个嵌套函数；
2. 嵌套函数必须引用封闭函数中传入参数或定义变量；
3. 封闭函数必须返回嵌套函数。

在封闭函数中传入的参数或定义的变量，我们称为环境变量（或自由变量）。在嵌套函数中使用环境变量需要使用关键字nonlocal声明。

环境变量的生命周期和封闭函数相同，将封闭函数赋值给变量后，此封闭函数中的环境变量就已经定义在内存中；嵌套函数可以直接调用环境变量。

### 环境变量

```python
m = 2

def fun(a):
    def add(b):
        nonlocal a
        a = a + b
        return a
    return add

f = fun(m)
print(f(2))
print(f(3))
print('m = %d'%m)
f1 = fun(m)
print(f1(4))
print(f1(5))
print('m = %d'%m)
'''
运行结果
4
7
m = 2
6
11
m = 2
'''
```

### 总结

1. 在封闭函数中传入参数为全局变量，则封闭函数会定义一个环境变量来保存全局变量的数值。因此在嵌套函数中修改其变量的数值，实际上修改的是在封闭函数中定义的环境变量，而不是全局变量。这也是在函数运行后，全局变量m依旧是3的原因。

2. 在[函数作为返回值](/articles/python/python3%20函数式编程?id=函数作为返回值)的例子中，执行f = fun(m)和f1 = fun(m)，表示将闭包的函数分别赋值给f和f1，f和f1的地址不同，因此f和f1中的环境变量也是不同的。

3. 方法1虽然使用全局变量也可以实现，但是在代码尽量不要过多的使用全局变量，以防名称覆盖以及参数调用错误。

4. 环境变量本质上属于局部变量，由于将闭包的函数赋值给变量，导致封闭函数的生命周期和变量一致，也因此环境变量的生命周期和赋值的变量一致。

### 使用闭包解决实际问题

在地图上，一位路人的坐标为(3,5)，其每次移动的相对坐标为(x,y)，求路人的绝对坐标

```python
m = [3,5]
def fun(a):
    def add(b):
        nonlocal a
        c = 0
        while c < len(b):
            a[c] = a[c] + b[c]
            c += 1
        return a
    return add


f = fun(m)
print(f([1, 2]))
print(f([6, 9]))
'''
运行结果
[4, 7]
[10, 16]
'''
```

## 高阶函数

### lambda表达式

lambda表达式又称为匿名函数，其语法格式：

```python
lambda parameter_list: expression
```

参数说明：

- lambda表示关键字；
- parameter_list为参数列表，参数之间用逗号隔开；
- expression为返回的表达式，且表达式只能有一个(注：只能是表达式，不能是语句；即不能包含赋值运算符)

```python
f = lambda x, y : x + y
print(f(1, 2))
'''
运行结果
3
'''
```

lambda表达式一般和高阶函数一起使用

### 三元表达式

三元表达式的语法格式：

```python
expression_T if expression else expression_F
```

若表达式expression为真，则执行expression_T；若未假，则执行expression_F

```python
a, b = 5, 2

ret = b - a if a < b else a - b

print(ret)
'''
运行结果
3
'''
```

### map方法

map方法又被称为映射，其语法格式：

```python
map(func, *iterables) --> map object
```

- func参数表示函数，其必须有返回值(若无返回值，则map返回序列中元素全是None)；
- *iterables是可变参数，用来表示多个序列、集合、字典；map object表示返回值是map对象。

**函数作用**：对*iterables中的每一个元素，都执行一次func函数

```python
list1 = [1,2,3,4,5,6]
list2 = [7,6,5,4,3,2,1]

def square(x):
    return x * x

def add(x, y):
    return x + y


r1 = map(square, list1)
print(r1)
print(list(r1))

r2 = map(add, list1, list2)
print(r2)
print(list(r2))
'''
运行结果
<map object at 0x000001E19496B7F0>
[1, 4, 9, 16, 25, 36]
<map object at 0x000001E19496B898>
[8, 8, 8, 8, 8, 8]
'''
```

当map函数中有多个序列时，选择元素最少的序列如list1，其长度作为map函数返回序列的长度。

为了简化代码，map中的func可以使用lambda表达式

```python
list1 = [1,2,3,4,5,6]
list2 = [7,6,5,4,3,2,1]

r1 = map(lambda x: x*x, list1)
print(r1)
print(list(r1))
r2 = map(lambda x, y: x+y, list1, list2)
print(r2)
print(list(r2))
'''
运行结果
<map object at 0x000001A6212AB7B8>
[1, 4, 9, 16, 25, 36]
<map object at 0x000001A6212AB860>
[8, 8, 8, 8, 8, 8]
'''
```

map函数的返回值也可以是其他数据类型，如列表、元组、集合。

### reduce函数

reduce函数又被称为归约，其语法格式：

```python
def reduce(function, sequence, initial)
```

- 参数function表示为函数（参数必须为2个）
- 参数sequence表示序列
- initial表示初始值

**注：使用reduce函数需要模块functools**

```python
from functools import reduce
list1 = [1, 2, 3, 4, 5, 6]

# 连续计算，连续调用lambda
r1 = reduce(lambda x, y: x+y, list1)
print(r1)
r2 = reduce(lambda x, y: x+y, list1, 10)
print(r2)
'''
运行结果
21
31
'''
```

reduce函数将序列sequence中的元素传递给函数function，每次传递2个。

- 若无initial，则默认initial为0；
- 若有initial，则在第一次传递参数时，作为第一个参数传递给函数function。当每次function执行结束后，其返回值作为下一次function运行的第一个参数。

因此reduce函数的作用是累积序列

### filter函数

filter函数语法格式：

```python
filter(function or None, iterable) --> filter object
```

- 参数function表示函数
- 参数iterable表示序列

序列中的每一个元素作为参数传递给函数进行判断，然后返回True或False，将返回True的元素存放到新列表中

```python
list1 = [1, 'a', 2, 'b', 3, 'c', 4, 'd', 5, 'e']

ret = filter(lambda x: isinstance(x, str), list1)
print(list(ret))
'''
运行结果
['a', 'b', 'c', 'd', 'e']
'''
```

### 装饰器

若封闭函数中传入参数是一个函数，该怎么办？

```python
def decorator(func):
    def wrapper():
        print('Start: '+func.__name__)
        func()
        print('End: '+func.__name__)
    return wrapper


def function_one():
    print('this is function_one')

f1 = decorator(function_one)

f1()
'''
运行结果
Start: function_one
this is function_one
End: function_one
'''
```

在不修改函数function_one的前提下，增加了function_one的功能，即在function_one执行前和执行结束分别打印函数名称（可用于调试）。函数decorator就可以称为一个**装饰器**。

由于使用f1 = decorator(function_one)和f1()来调用function_one比较麻烦，可以使用关键字@来实现装饰效果

```python
def decorator(func):
    def wrapper():
        print('Start: '+func.__name__)
        func()
        print('End: '+func.__name__)
    return wrapper

@decorator
def function_one():
    print('this is function_one')

function_one()
'''
运行结果
Start: function_one
this is function_one
End: function_one
'''
```

装饰器基本知识1：**一个函数用户可以有多个装饰器，装饰器的执行顺序从上到下**

```python
import time
def dec_time(func):
    def wrapper():
        print('time start')
        strat_time = time.time()
        func()
        end_time = time.time()
        print('Function runtime is %f'%(end_time-strat_time))
    return wrapper

def decorator(func):
    def wrapper():
        print('Start: '+func.__name__)
        func()
        print('End: '+func.__name__)
    return wrapper

@decorator
@dec_time
def function_one():
    print('this is function_one')
    
function_one()

'''
运行结果
Start: wrapper
time start
this is function_one
Function runtime is 0.000998
End: wrapper
'''
```

装饰器基本知识2：**被装饰的函数（function_one）也可以传入参数，为了表示参数的一般性，将wrapper参数设置为`*args`和`**kwargs`。其中，`*args`表示可变参数，`**kwargs`表示关键字可变参数**

```python
def decorator(func):
    def wrapper(*args, **kwargs):
        print('Start: ' + func.__name__)
        func(*args, **kwargs)
        print('End: ' + func.__name__)
    return wrapper


@decorator
def function_one():
    print('this is function_one')


@decorator
def function_two(name):
    print('this is function_two, name is ' + str(name))


@decorator
def function_three(*args, **kwargs):
    print('this is function_three', end=', ')
    print(args, end=', ')
    print(kwargs)


function_one()
function_two('function_two')
function_three('lisi', 'China', age=18, profession='student')
'''
运行结果
Start: function_one
this is function_one
End: function_one
Start: function_two
this is function_two, name is function_two
End: function_two
Start: function_three
this is function_three, ('lisi', 'China'), {'age': 18, 'profession': 'student'}
End: function_three
'''
```

装饰器的封闭函数中传入被装饰函数的函数名，嵌套函数中传入被装饰函数的参数

装饰器基本知识3：**装饰器也可以传入参数，但装饰器的具体实现需要修改**

```python
parm = 1.234

def decorator_parameter(parm):
    def decorator(func):
        def wrapper():
            print('Start: '+func.__name__)
            print('parm = %f'%parm)
            func()
            print('End: '+func.__name__)
        return wrapper
    return decorator


@decorator_parameter(parm)
def function_one():
    print('this is function_one')


function_one()
'''
运行结果
Start: function_one
parm = 1.234000
this is function_one
End: function_one
'''
```

对于3重封装的装饰器很难理解，实际上decorator_parameter(parm)函数执行后返回decorator，因此@decorator_parameter(parm) = @decorator