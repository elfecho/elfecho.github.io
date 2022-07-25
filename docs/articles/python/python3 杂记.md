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

