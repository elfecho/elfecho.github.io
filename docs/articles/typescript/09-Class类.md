
## 类的定义与继承

```javascript
// 类里写属性与方法
class Person {
  name = 'elfecho'
  getName() {
    return this.name
  }
}
const person = new Person()
console.log(person.getName()) // elfecho

// 继承类，继承类属于字类，被继承的属于父类
class Teacher extends Person {
  getTeacherName() {
    return 'elfecho Teacher'
  }
  // 子类可以重写父类的属性与方法
  getName() {
    // super 关键字指向了父类，可以直接调用父类。不会受到类重写的影响
    return super.getName() + 'TTT'
  }
}

const teacher = new Teacher()
console.log(teacher.getName()) // elfecho
console.log(teacher.getTeacherName()) // elfecho Teacher
```

## 修饰符

**访回类型：**

- private：允许在类内使用
- protected：允许在类内及继承的子类中使用
- public：允许在类的内外调用（默认）

**自带方法：**

- readonly：只读属性
- static：将方法挂载到类上而不是实例上，实例是没法访问和继承到的

### public

`public`表示公共的，用来指定在创建实例后可以通过实例访问的，也就是类定义的**外部**可以访问的属性和方法。

**类中的属性默认是 public**

```typescript
class Point {
  public x: number;
  public y: number;
  
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
  
  public getPosition() {
    return `(${this.x}, ${this.y})`;
  }
}
```

### private

`private`修饰符表示私有的，它修饰的属性在类的定义外面是没法访问的：

```typescript
class Parent {
  private age: number;
  constructor(age: number) {
    this.age = age;
  }
}

const p = new Parent(18);

console.log(p); // { age: 18 }
console.log(p.age); // error 属性“age”为私有属性，只能在类“Parent”中访问
console.log(Parent.age); // error 类型“typeof ParentA”上不存在属性“age”

class Child extends Parent {
  constructor(age: number) {
    super(age);
    console.log(super.age); // 通过 "super" 关键字只能访问基类的公共方法和受保护方法
  }
}
```

这里在 `constructor` 中访问 `super`，这的 `super` 相当于父类本身，这里我们看到使用 `private` 修饰的属性，在子类中是没法访问的。

### protected

`protected`修饰符是受保护修饰符，`protected`修饰的成员在**继承该类的子类**中可以访问。

我们再来看下上面那个例子，把父类 Parent 的 age 属性的修饰符 private 替换为 protected：

```typescript
class Parent {
  protected age: number;
  constructor(age: number) {
    this.age = age;
  }
  protected getAge() {
    return this.age;
  }
}

const p = new Parent(18);

console.log(p.age); // error 属性“age”为私有属性，只能在类“ParentA”中访问
console.log(Parent.age); // error 类型“typeof ParentA”上不存在属性“age”

class Child extends Parent {
  constructor(age: number) {
    super(age);
    console.log(super.age); // undefined
    console.log(super.getAge());
  }
}

new Child(18)
```