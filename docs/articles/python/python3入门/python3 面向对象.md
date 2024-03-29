# 面向对象

面向对象：世界上的每个人或事务都能看成一个对象，每个对象都有自己的属性和行为，对象与对象之间通过方法来交互。面向对象是一种以“对象”为中心的编程思想，把要解决的问题分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个对象在整个解决问题的步骤中的属性和行为。

- 优点：易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统 更加灵活、更加易于维护
- 缺点：性能比面向过程低

扩展：高内聚、低耦合

1. 内聚：模块内部的代码，例如方法，变量，对象，或者是功能模块。相互之间的联系越强，内聚就越高，模块的独立性就越好。一个模块应该尽量的独立，去完成独立的功能

2. 耦合：模块与模块之间有些操作是有关联的，例如水桶效应，一个水桶若是有一块木板比其他木板短，那么这个水桶装的水也就会相应减少，因此模块与模块之间的关系越是紧密，独立性就越不好，就容易产生连锁反应

3. 模块：就是将大型系统的复杂问题，分成不同的小模块，去处理问题

```mindmap
Python 类
	变量
		类变量
		实例变量
	方法
		实例方法
		类方法
		静态方法
	构造函数
	成员的可见性
	面向对象3大特性
		继承性
		封装性
		多态性
```

## 类和对象

1. **类** 是对一群具有**相同特征** 或者**行为**的事物的一个统称。是抽象的，不能直接使用。

2. 构成：三部分
		
		类的名称：类名  
		类的属性：一组数据  
		类的方法(行为)：允许对其进行操作的方法

		例子：
			名称：人  
			属性：姓名、身高、年纪  
			行为：吃饭、睡觉、上厕所

3. 抽象类：拥有相同(或者类似)属性和行为的对象都可以抽像出一个类

		例子：小明在公交车上牵着一只叼着热狗的狗
			小明 --- 人类  
			公交车 --- 交通工具类  
			热狗 --- 实物类  
			狗 --- 狗类

### 类中可以放置函数和变量

def语句定义一个函数，class语句定义一个类。

在类中被定义的函数被称为类的【方法】，描述的是这个类能做什么。使用类名.函数名()的格式，就可以让类的方法运行起来。

在类中被定义的变量被称为类的【属性】。使用类名.变量名的格式，可以把类中的属性的值提取出来。

使用类名.变量名的格式，可以让我们在类的外面，增加或修改类的属性。

为了让代码更直观，一般会把类中的函数和函数之间换行隔开。


### 构造函数

构造函数是一种特殊类型的方法(函数)，它在类的实例化对象时被调用。 构造函数通常用于初始化(赋值)给实例变量。 构造函数还验证有足够的资源来使对象执行任何启动任务。 

**创建一个构造函数**

构造函数是以双下划线(`__`)开头的类函数。构造函数的名称是`__init__()`。

创建对象时，如果需要，构造函数可以接受参数。当创建没有构造函数的类时，Python会自动创建一个不执行任何操作的默认构造函数。

每个类必须有一个构造函数，即使它只依赖于默认构造函数。


**构造函数特点**：
1. 实例化时自动执行一次构造函数
2. 只能返回None
3. 让模块可以生成不同的对象
4. 用于初始化对象的属性

```python
class Student():
    name = 'elfecho'  # 类变量
    age = 0
    total = 1
	
	# 实例方法
    def __init__(self, name, age):
        # 构造函数
        self.name = name  # 实例变量
        self.age = age
        print(Student.total) # 访问类变量
        print(self.__class__.total) # 访问类变量
        
	# 实例方法
    def do_homework(self):
        print('homework')


student1 = Student('喜小乐', 18)
print(student1.name)
print(Student.name)
print(student1.__dict__)  # 查询构造函数里变量的字典

# 运行结果
'''
1
1
喜小乐
elfecho
{'name': '喜小乐', 'age': 18}
'''
```

- 类变量只跟类是相关的
- 实例变量的定义使用 self 进行定义
- 实例方法里面访问类变量，使用`类名.xx`或者`self.__class__.xx`进行访问


### 类方法

- `@classmethod`是声明了下面的函数是类方法
- 类方法不需要 `self` 来表示自身了，而是用了 `cls` 来代替
- 对象也可以调用类方法，但是不建议这么做

```python
class Student():
    # 一个班级里所有的学生总数
    sum = 0

    def __init__(self, name, age):
        # 构造函数
        self.name = name
        self.age = age

    # 类方法
    @classmethod
    def plus_sum(cls):
        cls.sum += 1
        print('当前班级的人数为' + str(cls.sum))


student1 = Student('喜小乐', 18)
Student.plus_sum()
student2 = Student('石敢当', 19)
Student.plus_sum()
student1.plus_sum()  # 对象调用类方法

'''
# 运行
当前班级的人数为1
当前班级的人数为2
当前班级的人数为3
'''
```

### 静态方法

- `@staticmethod`也是通过 类名.函数名 的方式进行调用
- 静态方法不需要表示自身对象的`self`或者`cls`作为参数，跟直接使用函数是一样的

```python
class Student():
	@staticmethod
	def get_name(name):
		print('My name is ', name)


student1 = Student()
student1.get_name('elfecho')
'''
# 运行
My name is elfecho
'''
```

### 成员可见性

如果一个变量或者函数是公开的（`public`），就可以在类的外部进行访问，有时候为避免被公开访问，可以进行私有化（`private`）设置，这样在外部就不能进行访问。

python 变成私有的方法：
变量或者方法前加双下划线`__`
（前后都有双下划线是公开的，可以通过外部访问，但这种一般是python默认的一些方法，不建议自己的方法用这种命名法）

```python
class Student():
	def __init__(self, name, age):
		self.name = name
		self.age = age
		self.__score = 0  # 定义私有变量

	def marking(self, score):
		if (score < 0):
            return '不能够给同学打负分'

        self.__score = score
        print(self.name + '同学本次考试分数为' + str(self.__score))

student1 = Student('喜小乐', 18)
print(student1.__score)  # 直接打印私有变量会报错
print(student1.__dict__)  # {'name': '喜小乐', 'age': 18, '_Student__score': 89}

```

**深入挖掘python成员可见性：**
如果将一个变量定义为私有变量，python是在内部将这个私有变量换了一个名字，以达到无法引用有个私有变量的目的

如果对一个私有变量赋值，如：

```python
student1.__score = 59
print(student1.__score) # 59
```

由于动态语言的特性，不过是在对象中新增了一个`__score`，和原来的对象无关，所以可以直接访问并且赋值（没有什么是不可以访问的）

## 面向对象的三大特性

封装：提高程序的安全性。将数据（属性）和行为（方法）包装到类对象中，在方法内部对属性进行操作，在类对象的外部调用方法，这样无需关心方法内部的具体实现细节，从而隔离了复杂度；在python中没有专门的修饰符用于属性的私有，如果该属性不希望在类对象外部被访问，前边使用 `__`

继承：提高代码的复用性

多态：提高程序的可扩展性和可维护性

### 继承

语法格式

```python
class 子类类名(父类1,父类2,......):
	pass
```

**继承特点**：
- 如果一个类没有继承任何类，则默认继承object
- python支持多继承
- 定义子类时，必须在其构造函数中调用父类的构造函数

继承法：
-   需要定义子类时，使用`class 子类名(父类名):`
-   在实例化子类和调用这些子类里没有的构造函数、参数、方法时，会自动搜索父类里的
-   子类里的构造函数里，如果需要父类里的构造函数，需要调用  
    `父类.__init__(self.参数)`

实例化的时候，python会自动补上构造函数中的`self`，因此用对象调用实例方法时实参不需要写`self`，如果用类调用则需要。（不推荐用类来调用）

子类调用父类的方法：  
`super`关键字  
格式：`super(子类名， self).父类方法名(参数)`

```python
class Human(object):
    def __init__(self,name,age):
        self.name=name
        self.age=age
    def info(self):
        print(self.name,self.age)
 
class Student(Human):
    def __init__(self,name,age,school):
	    # Human.__init__(self, name, age) # 不建议用类来调用
        super().__init__(name,age)
        self.school=school
 
class Teacher(Human):
    def __init__(self,name,age,teachofyear):
        super().__init__(name,age)
        self.teachofyear=teachofyear
 
stu=Student('张三', 20, '人民路中学')
teach=Teacher('李四', 40, 20)
stu.info()
teach.info()
```

#### 方法重写

如果子类对继承父类中的某个属性或方法不满意，可以在子类中对其（方法体）进行重 新编写

子类重写后的方法中可以通过super().xxx()调用父类中被重写的方法