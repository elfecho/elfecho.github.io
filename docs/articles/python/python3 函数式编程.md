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
print(f.__closure__[0].cell_contents)  # 查看闭包里面的变量
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

