---
title: 学习JS (1)
published: 2024-12-14
description: "语法专题"
tags: ["JavaScript"]
category: Notes
draft: false
---

## 2. 语法专题
### 2.1 类型转换
#### 强制转换
**Number()**
使用Number函数，可以将任意类型的值转化成数值。
```javascript
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
```
其转换规则如下：
- 第一步，调用对象自身的`valueOf`方法。如果返回原始类型的值，则直接对该值使用`Number`函数，不再进行后续步骤。
- 第二步，如果`valueOf`方法返回的还是对象，则改为调用对象自身的toString方法。如果`toString`方法返回原始类型的值，则对该值使用Number函数，不再进行后续步骤。
- 第三步，如果`toString`方法返回的是对象，就报错。

**String()**
String函数可以将任意类型的值转化成字符串。
```javascript
String(123) // "123"
String('abc') // "abc"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```
其转换规则如下：
- 先调用对象自身的toString方法。如果返回原始类型的值，则对该值使用String函数，不再进行以下步骤。
- 如果toString方法返回的是对象，再调用原对象的valueOf方法。如果valueOf方法返回原始类型的值，则对该值使用String函数，不再进行以下步骤。
- 如果valueOf方法返回的是对象，就报错。

**Boolean()**

Boolean()函数可以将任意类型的值转为布尔值，除了以下五个值的转换结果为false，其他的值全部为true。
```javascript
Boolean(undefined) // false
Boolean(null) // false
Boolean(0) // false
Boolean(NaN) // false
Boolean('') // false
```

**自动转换**
遇到以下三种情况时，JavaScript 会自动转换数据类型，即转换是自动完成的，用户不可见。
- 第一种情况，不同类型的数据互相运算。
```javascript
123 + 'abc' // "123abc"
```
- 第二种情况，对非布尔值类型的数据求布尔值。
```javascript
if ('abc') {
    console.log('hello')
}  // "hello"
```
- 第三种情况，对非数值类型的值使用一元运算符（即+和-）。
```javascript
123 + 'abc' // "123abc"
```
自动转换的规则是这样的：预期什么类型的值，就调用该类型的转换函数。比如，某个位置预期为字符串，就调用String()函数进行转换。如果该位置既可以是字符串，也可能是数值，那么默认转为数值。

### 2.2 异常处理
与python try... catch... final... 类似

### 2.3 Object的实例方法

---

### Object.prototype 方法概览
**实例方法**定义在 `Object.prototype` 上，所有对象实例继承这些方法。常用方法包括：

1. **`valueOf()`**：返回对象的原始值，默认返回对象本身。
   - 示例：
     ```js
     var obj = new Object();
     obj.valueOf() === obj; // true
     ```
   - 自定义 `valueOf` 可影响对象的运算行为：
     ```js
     obj.valueOf = () => 2;
     1 + obj; // 3
     ```

2. **`toString()`**：返回对象的字符串形式，默认返回 `"[object Object]"`。
   - 示例：
     ```js
     var obj = {};
     obj.toString(); // "[object Object]"
     ```
   - 自定义 `toString` 改变字符串化结果：
     ```js
     obj.toString = () => "custom";
     String(obj); // "custom"
     ```
   - 可通过 `Object.prototype.toString.call(value)` 判断值的类型：
     ```js
     Object.prototype.toString.call(2) // "[object Number]"
     Object.prototype.toString.call('') // "[object String]"
     Object.prototype.toString.call(true) // "[object Boolean]"
     Object.prototype.toString.call(undefined) // "[object Undefined]"
     Object.prototype.toString.call(null) // "[object Null]"
     Object.prototype.toString.call(Math) // "[object Math]"
     Object.prototype.toString.call({}) // "[object Object]"
     Object.prototype.toString.call([]) // "[object Array]"
     ```
     上面代码表示对value这个值调用`Object.prototype.toString`方法。

3. **`toLocaleString()`**：返回对象的本地化字符串形式。
   - 示例：
     ```js
     var person = {
       toString: () => "Global",
       toLocaleString: () => "Localized"
     };
     person.toString(); // "Global"
     person.toLocaleString(); // "Localized"
     ```

4. **`hasOwnProperty()`**：判断属性是否为对象自身的属性。
   - 示例：
     ```js
     var obj = { p: 123 };
     obj.hasOwnProperty("p"); // true
     obj.hasOwnProperty("toString"); // false
     ```

5. **其他方法（未详述）**：
   - `isPrototypeOf()`：判断当前对象是否为另一个对象的原型。
   - `propertyIsEnumerable()`：判断某属性是否可枚举。
