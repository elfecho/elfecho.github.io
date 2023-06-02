- 接口一方面可以在面向对象编程中表示为行为的抽象，另外可以用来描述对象的现状
- 接口就是把一些类中共有的属性和方法抽象出来，可以用来约束实现此接口的类
- 一个类可以实现多个接口，一个接口也可以被多个类实现，但一个类可以有多个子类，但只能有一个父类
- 同名的接口可以写多个，类型会自动合并

## 基本用法

```typescript
// 示例 A
declare const myPoint: { x: number; y: number };

// 示例 B
interface Point {
  x: number;
  y: number;
}
declare const myPoint: Point;
```

示例 B 的好处在于，如果有人创建了一个基于 `myPoint` 的库来添加新成员, 那么他可以轻松将此成员添加到 `myPoint` 的现有声明中:

```typescript
// Lib a.d.ts
interface Point {
  x: number,
  y: number
}
declare const myPoint: Point

// Lib b.d.ts
interface Point {
  z: number
}

// Your code
myPoint.z // Allowed!
```

TypeScript 接口是开放式的，这是 TypeScript 的一个重要原则，它允许你使用接口来模仿 JavaScript 的可扩展性。

> 由于TypeScript的声明合并策略，会将同名的一些可合并的声明进行合并，当同名的两个值或类型不能合并的时候，就会报错；或者可以合并的连个同名的值不符合要求，也会有问题。

注意：

- 不要把Interface理解为是在定义一个对象，而要理解为`{}`括号包裹的是一个代码块，里面是一条条声明语句，只不过声明的不是变量的值而是类型；
- 声明也不用等号赋值，而是冒号指定类型；
- 每条声明之前用换行分隔即可，或者也可以使用分号或者逗号，都是可以的。

## 扩展用法

### 函数类型

接口可以描述普通对象，还可以描述函数类型，我们先看写法：

```typescript
interface AddFunc {
  // 调用签名: 由带有参数类型的参数列表和返回值类型组成
  (num1: number, num2: number): number;
}

const add: AddFunc = (n1, n2) => n1 + n2;
const join: AddFunc = (n1, n2) => `${n1} ${n2}`; // 不能将类型'string'分配给类型'number'
add("a", 2); // 类型'string'的参数不能赋给类型'number'的参数
```

### 索引类型

可以使用接口描述索引的类型和通过索引得到的值的类型，比如一个数组`['a', 'b']`，数字索引`0`(数字类型)对应的通过索引得到的值为`'a'`（字符）。我们可以同时给索引和值都设置类型，看下面的示例：

```typescript
interface RoleDic {
  [id: number]: string;
}

const role1: RoleDic = {
  0: "super_admin",
  1: "admin"
};


const role2: RoleDic = {
  s: "super_admin",  // error 不能将类型"{ s: string; a: string; }"分配给类型"RoleDic"。
  a: "admin"
};

const role3: RoleDic = ["super_admin", "admin"];
// console.log(role3.length)
// 当定义了索引类型之后，数组的length方法，将不存在，包括Array原型链上的其他方法也不存在了
```

也可以给索引设置`readonly`，从而防止索引返回值被修改。

```typescript
interface RoleDic {
  readonly [id: number]: string;
}

const role: RoleDic = {
  0: "super_admin"
};

role[0] = "admin"; // error 类型"RoleDic"中的索引签名仅允许读取
```

### 绕开多余属性检查
