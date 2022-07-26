## 用字典映射代替switch case语句

比如JavaScript里使用的switch

```javascript
day = 1
switch(day) {
	case 0:
		dayName = 'Sunday'
		break
	case 1:
		dayName = 'Monday'
		break
	case 2:
		dayName = 'Thesday'
		break
	...
	default:
		dayName = 'Unkown'
		break
}
```

python中由于没有switch方法，我们可以使用字典的映射来代替这个方法

```python
day = 0

switcher = {
    0: 'Sunday',
    1: 'Monday',
    2: 'Tuesday'
}

day_name = switcher[day]
print(day_name)
```

这样写有个问题，就是如果day编写的不是里面所涵盖的话就会报错，由于python的灵活性，我们可以通过以下改写而实现这个方法

```python
day = 6

switcher = {
    0: 'Sunday',
    1: 'Monday',
    2: 'Tuesday'
}

day_name = switcher.get(day, 'Unkown')

print(day_name)
```

如果需要加一些逻辑进行映射的话，可以使用函数的方式进行实现

```python
day = 1
def get_sunday():
    return 'Sunday'

def get_monday():
    return 'Monday'

def get_tuesday():
    return 'Tuesday'

def get_default():
    return 'Unkown'

switcher = {
    0: get_sunday,
    1: get_monday,
    2: get_tuesday
}

day_name = switcher.get(day, get_default)()

print(day_name)
```

## 列表推导式

列表推导式不单单只能对列表进行推导，set、字典、元组都是可以被推导的

```python
a = [1,2,3,4,5,6,7,8]
# 实现a里面的元素平方，用列表推导式实现
b = [i**2 for i in a] 
# 如果需要进行过滤一些条件的话，列表推导式就可以很大的起到作用了
b1 = [i**2 for i in a if i >= 5]

c = (1,2,3,4,5,6,7,8)
c1 = (i**2 for i in c if i >= 5) # 这里返回的是一个可遍历的object
c2 = [i**2 for i in c if i >= 5]

s = {1,2,3,4,5,6,7,8}
s1 = [i**2 for i in s if i >= 5]
print(b)
print(b1)
print(c1, list(c1))
print(c2)
print(d1)
'''
运行结果
[1, 4, 9, 16, 25, 36, 49, 64]
[25, 36, 49, 64]
<generator object <genexpr> at 0x0000027011D7C2B0> [25, 36, 49, 64]
[25, 36, 49, 64]
[25, 36, 49, 64]
'''
```

如何实现字典的列表推导式，可以看下面这个例子

```python
students = {
    '喜小乐': 18,
    '石敢当': 20,
    '黄小五': 15,
}

b = {value: key for key, value in students.items()}
c = [key for key, value in students.items()]

print(b)
print(c)
'''
运行结果
{18: '喜小乐', 20: '石敢当', 15: '黄小五'}
['喜小乐', '石敢当', '黄小五']
'''
```

<<<<<<< HEAD
## 迭代器



## 生成器
=======
## 迭代器 Iterator

Python中一个可迭代对象(iterable object)是一个实现了`__iter__`方法的对象，它应该返回一个迭代器对象(iterator object)。迭代器是一个实现`__next__`方法的对象，它应该返回它的可迭代对象的下一个元素，并在没有可用元素时触发StopIteration异常。

当我们使用for循环遍历任何可迭代对象时，它在内部使用iter()方法获取迭代器对象，该迭代器对象进一步使用next()方法进行迭代。此方法触发StopIteration以表示迭代结束。

Python中大多数内置容器，如列表、元组、字符串等都是`可迭代对象`的。它们是iterable但不是iterator，把它们从iterable变成iterator可以使用iter()函数

```python
import copy
class BookCollection:
    def __init__(self):
        self.data = ['《往事》', '《只能》', '《回味》']
        self.cur = 0
        pass

    def __iter__(self):
        return self

    def __next__(self):
        if self.cur >= len(self.data):
            raise StopIteration()
        r = self.data[self.cur]
        self.cur += 1
        return r


books = BookCollection()
# books_copy = copy.copy(books)  # 浅拷贝
books_copy = copy.deepcopy(books)  # 深拷贝

print(next(books))
print(next(books))
print(next(books))

for book in books_copy:
    print(book)
    
'''
运行结果
《往事》
《只能》
《回味》
《往事》
《只能》
《回味》
'''
```

## 生成器 Generator

如果我们需要打印1到10000的数字，我可以使用之前学过的列表推导式进行编写

```python
n = [i for i in range(0, 10001)]
print(n)
for i in n:
	print(i)
```
这个n是非常占用内存的，如果需要打印1到1亿的话，把这个变量放在内存里，是非常消耗性能的，那这个时候我们可以使用生成器来解决这个问题

```python
def gen(max):
    n = 0
    while n <= max:
        print(n)
        n += 1
        
gen(10000)
```

这里写法的优势就是没有过度消耗计算机里的内存，每次打印都是实时计算出来的结果，很遗憾的是这个只是一个函数，还不是一个生成器，可以通过以下改写实现生成器

```python
def gen(max):
    n = 0
    while n <= max:
        print(n)
        yield n
        n += 1
        
g = gen(10000)
print(g)
'''
运行结果
<generator object gen at 0x0000022748C69258>
'''
```

```python
# 使用方式一：
print(next(g)) # 同迭代器的方式一样调用
print(next(g))
# 使用方式二：
for i in g:
	print(i)
```

或者利用此函数以及 Iterator expression，你可以用很紧凑的代码构造出很多**迭代数列**：

```python
squares = (x * x for x in range(n))
evens = (2 * x for x in range(n))
```

**总结概念**：

- Generator Function：含有 yield 关键字的**函数**，会返回一系列值，可以使用 next() 对其返回值进行迭代。

- Generator Iterator：generator function 返回的**对象**。可以进行一次性地迭代。

- Generator Expression：可以求值为 generator iterator 的**表达式**。使用小括号和 for 来定义, 如下面例子

  ```python
  a = (i*i for i in range(10))
  print(type(a))
  '''
  运行结果
  <class 'generator'>
  '''
  ```

## 空 None

-   None是python中的一个特殊的常量，表示一个空的对象。
-   **数据为空并不代表是空对象**，例如空列表、空字符串、0、False等**都不是None**。
-   None有自己的数据类型NontType，你可以将None赋值给任意对象，但是不能创建一个NoneType对象