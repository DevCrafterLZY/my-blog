---
title: 学习JS (1)
published: 2024-12-13
description: "数据类型和运算符"
tags: ["JavaScript"]
category: Notes
draft: false
---

## 1. 数据类型

### 1.1. typeof
`typeof` 运算符用于检查变量的数据类型，返回值为字符串，常见的返回值有：

1. **基本类型**：
    - `number`: 数值
    - `string`: 字符串
    - `boolean`: 布尔值
    - `undefined`: 未定义
    - `function`: 函数

2. **对象类型**：
    - `object`: 对象和数组（包括 `null`）

3. **未声明的变量**：
    - `undefined`: 未声明的变量

:::tip
- `typeof null` 返回 `"object"`，这是历史遗留问题。
- `typeof` 对数组和普通对象都返回 `"object"`，但函数返回 `"function"`。
  :::


### 1.2. 类型转换
使用 parse类型名(), eg: parseInt(),
数字系列的最好使用Number.parseInt()


### 1.3. String
`length`属性返回字符串的长度，该属性也是无法改变的。
```javascript
var s = 'hello';
s.length // 5

s.length = 3;
s.length // 5

s.length = 7;
s.length // 5
```
### 1.4. 对象
JavaScript中对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。
```javascript
var obj = {
  p: 123,
  m: function () { ... },
}
```
#### 1.4.1 对象的引用
如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。
```javascript
var o1 = {};
var o2 = o1;

o1.a = 1;
o2.a // 1

o2.b = 2;
o1.b // 2
```
但是，这种引用只局限于对象，如果两个变量指向同一个原始类型的值。那么，变量这时都是值的拷贝。
```javascript
var x = 1;
var y = x;

x = 2;
y // 1
```

#### 1.4.2 对象的操作

##### 属性的读取

读取对象属性有两种方式：点运算符和方括号运算符。

```js
var obj = { p: 'Hello World' };
obj.p // "Hello World"
obj['p'] // "Hello World"
```
数字键可省略引号，但不支持点运算符。

```js
var obj = { 0.7: 'Hello World' };
obj['0.7'] // "Hello World"
obj[0.7] // "Hello World"
obj.123 // 报错
```

##### 属性的赋值

点运算符和方括号运算符可用来为属性赋值。

```js
var obj = {};
obj.foo = 'Hello';
obj['bar'] = 'World';
```

##### 属性的查看

使用 `Object.keys()` 获取对象的所有属性。

```js
var obj = { key1: 1, key2: 2 };
Object.keys(obj); // ["key1", "key2"]
```

##### 属性的删除

使用 `delete` 删除属性，成功时返回 `true`。

```js
var obj = { p: 1 };
delete obj.p // true
obj.p // undefined
```

删除不存在的属性不会报错，返回 `true`。

```js
delete obj.p // true
```

`delete` 不删除继承的属性。

```js
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

##### 属性是否存在：`in` 运算符

`in` 运算符检查对象是否有某个属性，返回 `true` 或 `false`。

```js
'p' in obj // true
'toString' in obj // true
```

使用 `hasOwnProperty()` 判断属性是否为对象自身的。

```js
var obj = {};
'toString' in obj // true
obj.hasOwnProperty('toString') // false
```

##### 属性的遍历：`for...in` 循环

`for...in` 遍历对象的所有属性（包括继承的属性），但跳过不可遍历的属性。

```js
var obj = { a: 1, b: 2, c: 3 };
for (var i in obj) {
  console.log(i); // a, b, c
}
```

使用 `hasOwnProperty()` 确保只遍历对象自身的属性。

```js
for (var key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(key); // a, b, c
  }
}
```
### 1.5 函数
#### 1.5.1 函数声明
1. `function` 命令（函数声明）

```js
function print(s) {
  console.log(s);
}
```

通过 `function` 命令声明函数，包含函数名、参数列表和函数体。函数声明可在定义之前调用（函数提升）。

2. 函数表达式

```js
var print = function(s) {
  console.log(s);
};
```

函数表达式将匿名函数赋值给变量，函数名仅在函数内部有效。若在函数体内使用名称（如 `x`），该名称只在函数内部可用。

```js
var print = function x() {
  console.log(typeof x);
};

x // ReferenceError: x is not defined
print() // function
```

函数表达式需要在语句末尾加分号，声明则不需要。两者差异较小，基本可以视为等价。

#### 1.5.2 函数的属性和方法

1. `name` 属性

函数的 `name` 属性返回函数的名称。

```js
function f1() {}
f1.name // "f1"

var f2 = function() {};
f2.name // "f2"

var f3 = function myName() {};
f3.name // "myName"
```

对于函数表达式，若是匿名函数，返回变量名；若是具名函数，返回函数名。

2. `length` 属性

`length` 属性返回函数的参数个数。

```js
function f(a, b) {}
f.length // 2
```

`length` 始终等于函数定义时的参数个数，与调用时传入的参数数量无关。

3. `toString()` 方法

`toString()` 方法返回函数的源码。

```js
function f() {
  a();
  b();
  c();
}
f.toString()
// "function f() { a(); b(); c(); }"
```

原生函数的 `toString()` 返回提示信息：

```js
Math.sqrt.toString()
// "function sqrt() { [native code] }"
```

通过 `toString()`，也可以获取函数内部的注释或多行字符串：

```js
function f() {/*
  这是一个
  多行注释
*/}
f.toString()
// "function f(){/* 这是一个 多行注释 */}"
```

### 1.6 数组
JS中数组的本质是对象。
**in运算符**
in运算符与想象中的不同
```javascript
var arr = [ 'a', 'b', 'c' ];
2 in arr  // true
'2' in arr // true
4 in arr // false
```

### 1.7 算术运算符
- 加法运算符：x + y
- 减法运算符： x - y
- 乘法运算符： x * y
- 除法运算符：x / y
- 指数运算符：x ** y
- 余数运算符：x % y
- 自增运算符：++x 或者 x++
- 自减运算符：--x 或者 x--
- 数值运算符： +x
- 负数值运算符：-x

### 1.8. 比较运算符
**严格相等和严格不等**：
===, !==比较值和类型，判断值和类型都相等或都不相等。

:::tip
`NaN`不等于任何值，包括它本身。
```javascript
NaN === NaN
```
:::

**严格相等和严格不等**：
===, !==比较值，若类型不相同会尝试先转换为相同类型，判断值相等或不相等。
