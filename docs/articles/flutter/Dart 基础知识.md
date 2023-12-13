## 程序入口

JavaScript没有预定义的入口函数，但在Dart中，每个app都必须有一个顶级的 man ()函数作为应用程序的入口点
```Dart
// Dart
main(){
}
```

[DartPad](https://dartpad.dev/)是Dart的一个线上playground，提供Dart线上playground还有:[Online Dart Compiler](https://www.tutorialspoint.com/)。

## 控制台输出
要在Dart中打印到控制台，可使用 print

```Dart
//JavaScript
console.log("Hello world!");
// Dart
print('Hello world!');
```

## 变量
**Dart是类型安全的**-它使用静态类型检查和运行时的组合,检查以确保变量的值始终与变量的静态值匹配 类型。尽管类型是必需的，但某些类型注释是可选的，因为 Dat会执行类型推断
**创建和分配变量**
在JavaScript中，无法定义变量类型
在 Dart中,变量必须是明确的 类型或系统能够解析的类型

```Dart
// JavaScript
var name="JavaScript";
// Dart
String name = 'dart'; // Explicitly typed as a string.
var otherName = Dart'; // Inferred string.
// Both are acceptable in Dart.
```