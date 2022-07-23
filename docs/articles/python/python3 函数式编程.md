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

