---
title: 学习JS (3)
published: 2024-12-14
description: "标准库"
tags: ["JavaScript"]
category: Notes
draft: false
---

## 3. 标准库
### 3.1 Object对象
#### Object
`Object`本身是一个函数，可以当作工具方法使用，将任意值转为对象。这个方法常用于保证某个值一定是对象。

如果参数是原始类型的值，`Object`方法将其转为对应的包装对象的实例。
```javascript
var obj = Object(1);
obj instanceof Object // true
obj instanceof Number // true

var obj = Object('foo');
obj instanceof Object // true
obj instanceof String // true

var obj = Object(true);
obj instanceof Object // true
obj instanceof Boolean // true
```
如果`Object`方法的参数是一个对象，它总是返回该对象，即不用转换。
```javascript
var arr = [];
var obj = Object(arr); // 返回原数组
obj === arr // true

var value = {};
var obj = Object(value) // 返回原对象
obj === value // true

var fn = function () {};
var obj = Object(fn); // 返回原函数
obj === fn // true
```
利用这一点，可以写一个判断变量是否为对象的函数。
```javascript
function isObject(value) {
  return value === Object(value);
}

isObject([]) // true
isObject(true) // false
```
### Object.prototype方法 (实例方法)
:::tips
静态方法可以理解为类函数，整个类共用这个方法

实例方法是普通的成员函数，归对象所有可以使用this指针
:::

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

### 3.2 属性描述对象
JavaScript 提供了一个内部数据结构，用来描述对象的属性，控制它的行为，比如该属性是否可写、可遍历等等。这个内部数据结构称为“属性描述对象”（attributes object）。

下面是属性描述对象的一个例子。

```javascript
{
  value: 123,
  writable: false,
  enumerable: true,
  configurable: false,
  get: undefined,
  set: undefined
}
```

#### 属性描述对象6个元属性

（1）`value`

`value`是该属性的属性值，默认为`undefined`。

（2）`writable`

`writable`是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为`true`。

（3）`enumerable`

`enumerable`是一个布尔值，表示该属性是否可遍历，默认为`true`。如果设为`false`，会使得某些操作（比如`for...in`循环、`Object.keys()`）跳过该属性。

（4）`configurable`

`configurable`是一个布尔值，表示属性的可配置性，默认为`true`。如果设为`false`，将阻止某些操作改写属性描述对象，比如无法删除该属性，也不得改变各种元属性（`value`属性除外）。也就是说，`configurable`属性控制了属性描述对象的可写性。

（5）`get`

`get`是一个函数，表示该属性的取值函数（getter），默认为`undefined`。

（6）`set`

`set`是一个函数，表示该属性的存值函数（setter），默认为`undefined`。


#### 存取器 (get, set)

除了直接定义以外，属性还可以用存取器（accessor）定义。其中，存值函数称为`setter`，使用属性描述对象的`set`属性；取值函数称为`getter`，使用属性描述对象的`get`属性。

一旦对目标属性定义了存取器，那么存取的时候，都将执行对应的函数。利用这个功能，可以实现许多高级特性，比如定制属性的读取和赋值行为。

```javascript
var obj = Object.defineProperty({}, 'p', {
  get: function () {
    return 'getter';
  },
  set: function (value) {
    console.log('setter: ' + value);
  }
});

obj.p // "getter"
obj.p = 123 // "setter: 123"
```

上面代码中，`obj.p`定义了`get`和`set`属性。`obj.p`取值时，就会调用`get`；赋值时，就会调用`set`。

JavaScript 还提供了存取器的另一种写法。

```javascript
// 写法二
var obj = {
  get p() {
    return 'getter';
  },
  set p(value) {
    console.log('setter: ' + value);
  }
};
```

上面两种写法，虽然属性`p`的读取和赋值行为是一样的，但是有一些细微的区别。第一种写法，属性`p`的`configurable`和`enumerable`都为`false`，从而导致属性`p`是不可遍历的；第二种写法，属性`p`的`configurable`和`enumerable`都为`true`，因此属性`p`是可遍历的。实际开发中，写法二更常用。

注意，取值函数`get`不能接受参数，存值函数`set`只能接受一个参数（即属性的值）。

存取器往往用于，属性的值依赖对象内部数据的场合。

```javascript
var obj ={
  $n : 5,
  get next() { return this.$n++ },
  set next(n) {
    if (n >= this.$n) this.$n = n;
    else throw new Error('新的值必须大于当前值');
  }
};

obj.next // 5

obj.next = 10;
obj.next // 10

obj.next = 5;
// Uncaught Error: 新的值必须大于当前值
```

上面代码中，`next`属性的存值函数和取值函数，都依赖于内部属性`$n`。

### 3.2 Array对象


| **函数**             | **功能描述**                                                                                   | **是否改变原数组** | **备注**                                                                                                                                                       |
|----------------------|-----------------------------------------------------------------------------------------------|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Array.isArray()`    | 判断参数是否为数组，返回布尔值。                                                                | 否                 | 弥补 `typeof` 的不足，只能判断是否为数组。                                                                                                                      |
| `valueOf()`          | 返回数组本身。                                                                                 | 否                 | 通用方法，适用于所有对象。                                                                                                                                    |
| `toString()`         | 返回数组的字符串形式。                                                                          | 否                 | 嵌套数组会被展开为逗号分隔的字符串。                                                                                                                            |
| `push()`             | 在数组末端添加一个或多个元素，返回数组长度。                                                     | 是                 | 用于栈结构（后进先出）。                                                                                                                                      |
| `pop()`              | 删除数组最后一个元素并返回该元素。                                                              | 是                 | 用于栈结构（后进先出）。                                                                                                                                      |
| `shift()`            | 删除数组的第一个元素并返回该元素。                                                              | 是                 | 用于队列结构（先进先出）。                                                                                                                                    |
| `unshift()`          | 在数组开头添加一个或多个元素，返回数组长度。                                                     | 是                 | 用于队列结构（先进先出）。                                                                                                                                    |
| `join()`             | 使用指定分隔符连接数组元素为字符串。                                                             | 否                 | `undefined`、`null` 和空位会转为空字符串。                                                                                                                     |
| `concat()`           | 合并多个数组或其他值，返回新数组。                                                              | 否                 | 返回新数组，原数组不变。对象合并为浅拷贝。                                                                                                                     |
| `reverse()`          | 颠倒数组元素顺序，返回改变后的数组。                                                            | 是                 | 原数组顺序被改变。                                                                                                                                             |
| `slice()`            | 提取数组的一部分，返回新数组。                                                                  | 否                 | 支持负数索引，`slice()` 无参数时返回数组拷贝。                                                                                                                  |
| `splice()`           | 删除或添加数组元素，返回被删除的元素数组。                                                       | 是                 | 支持删除和插入元素。                                                                                                                                          |
| `sort()`             | 对数组进行排序，返回排序后的数组。                                                              | 是                 | 默认字典顺序排序，自定义排序需提供比较函数。                                                                                                                    |
| `map()`              | 对数组元素执行函数并返回新数组。                                                                | 否                 | 回调函数接受 3 个参数：当前值、索引、原数组。                                                                                                                  |
| `forEach()`          | 遍历数组，对每个元素执行指定操作。                                                               | 否                 | 无返回值，无法中断遍历。                                                                                                                                       |
| `filter()`           | 过滤数组，返回符合条件的新数组。                                                                | 否                 | 回调函数接受 3 个参数：当前值、索引、原数组。                                                                                                                  |
| `some()`             | 判断数组中是否至少有一个元素满足条件，返回布尔值。                                               | 否                 | 适合存在性检查；空数组返回 `false`。                                                                                                                          |
| `every()`            | 判断数组中是否所有元素都满足条件，返回布尔值。                                                   | 否                 | 空数组返回 `true`。                                                                                                                                           |
| `reduce()`           | 从左到右累计处理数组，最终返回一个值。                                                           | 否                 | 可指定初始值，适用于累加、累计等操作。                                                                                                                        |
| `reduceRight()`      | 从右到左累计处理数组，最终返回一个值。                                                           | 否                 | 类似 `reduce`，但遍历方向相反。                                                                                                                               |
| `indexOf()`          | 查找指定元素在数组中的首次出现位置，返回索引或 `-1`。                                             | 否                 | 不支持查找 `NaN`。                                                                                                                                          |
| `lastIndexOf()`      | 查找指定元素在数组中的最后一次出现位置，返回索引或 `-1`。                                          | 否                 | 不支持查找 `NaN`。                                                                                                                                          |


### 3.3 包装方法

所谓“包装对象”，指的是与数值、字符串、布尔值分别相对应的`Number`、`String`、`Boolean`三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。
```javascript
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);

typeof v1 // "object"
typeof v2 // "object"
typeof v3 // "object"

v1 === 123 // false
v2 === 'abc' // false
v3 === true // false
```

包装对象可以自定义方法和属性，供原始类型的值直接调用。

比如，我们可以新增一个`double`方法，使得字符串和数字翻倍。

```javascript
String.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

'abc'.double()
// abcabc

Number.prototype.double = function () {
  return this.valueOf() + this.valueOf();
};

(123).double() // 246
```
### 3.4 RegExp对象

以下是正则表达式中常用符号及其含义（ES6 兼容）：

**基础符号**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `.`       | 匹配除换行符以外的任意单个字符                                                                                                           | `/a.c/` 能匹配 `abc`、`a2c`      |
| `\d`      | 匹配任意数字（等价于 `[0-9]`）                                                                                                          | `/\d+/` 能匹配 `123`             |
| `\D`      | 匹配任意非数字字符                                                                                                                      | `/\D+/` 能匹配 `abc`             |
| `\w`      | 匹配任意字母、数字或下划线（等价于 `[a-zA-Z0-9_]`）                                                                                     | `/\w+/` 能匹配 `hello_123`       |
| `\W`      | 匹配任意非字母、数字或下划线字符                                                                                                        | `/\W+/` 能匹配 `#@$`             |
| `\s`      | 匹配空白字符（空格、制表符、换页符等）                                                                                                   | `/\s+/` 能匹配空格和换行         |
| `\S`      | 匹配任意非空白字符                                                                                                                      | `/\S+/` 能匹配 `abc`             |
| `\b`      | 匹配单词边界                                                                                                                            | `/\bword\b/` 仅匹配完整单词 `word` |
| `\B`      | 匹配非单词边界                                                                                                                          | `/\Bword/` 能匹配 `password` 中的 `word` |
| `\`       | 转义字符，用于匹配元字符的字面值                                                                                                       | `/\./` 能匹配 `.`                |

---

**量词**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `*`       | 匹配前面的字符 **0 次或多次**                                                                                                           | `/a*/` 能匹配 `a`、`aaa` 或空字符 |
| `+`       | 匹配前面的字符 **1 次或多次**                                                                                                           | `/a+/` 能匹配 `a`、`aaa`          |
| `?`       | 匹配前面的字符 **0 次或 1 次**                                                                                                          | `/a?/` 能匹配 `a` 或空字符        |
| `{n}`     | 匹配前面的字符 **恰好 n 次**                                                                                                           | `/a{3}/` 仅匹配 `aaa`            |
| `{n,}`    | 匹配前面的字符 **至少 n 次**                                                                                                           | `/a{2,}/` 匹配 `aa`、`aaa`       |
| `{n,m}`   | 匹配前面的字符 **至少 n 次，至多 m 次**                                                                                                | `/a{1,3}/` 匹配 `a`、`aa` 或 `aaa` |

---

**位置匹配**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `^`       | 匹配字符串开头                                                                                                                          | `/^a/` 仅匹配以 `a` 开头的字符串 |
| `$`       | 匹配字符串结尾                                                                                                                          | `/a$/` 仅匹配以 `a` 结尾的字符串 |
| `(?=...)` | **正向前瞻**，要求后面是指定内容，但不会捕获内容                                                                                         | `/a(?=b)/` 匹配 `ab` 中的 `a`    |
| `(?!...)` | **负向前瞻**，要求后面不是指定内容                                                                                                      | `/a(?!b)/` 匹配 `ac` 中的 `a`    |
| `(?<=...)`| **正向后顾**，要求前面是指定内容（ES2018 引入）                                                                                         | `/(?<=\$)\d+/` 匹配 `$100` 中的 `100` |
| `(?<!...)`| **负向后顾**，要求前面不是指定内容（ES2018 引入）                                                                                       | `/(?<!\$)\d+/` 匹配 `abc123` 中的 `123` |

---

**字符类**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `[abc]`   | 匹配方括号内任意字符                                                                                                                    | `/[abc]/` 匹配 `a`、`b` 或 `c`    |
| `[^abc]`  | 匹配方括号内 **未出现** 的任意字符                                                                                                       | `/[^abc]/` 匹配除 `a`、`b`、`c` 外的任意字符 |
| `[a-z]`   | 匹配指定范围内的字符                                                                                                                    | `/[a-z]/` 匹配小写字母            |
| `[0-9]`   | 匹配数字范围                                                                                                                            | `/[0-9]/` 匹配数字 0 到 9         |

---

**分组与引用**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `()`      | 分组，用于提取匹配或控制优先级                                                                                                          | `/(ab)+/` 匹配 `abab`            |
| `(?:...)` | 非捕获分组，不保存匹配结果                                                                                                              | `/(?:ab)+/` 仅匹配，不保存        |
| `\1`、`\2`| 引用分组的匹配结果                                                                                                                      | `/(\w)\1/` 匹配重复字符，如 `aa`  |
| `(?<name>...)` | **命名捕获组**，可以为分组命名（ES2018 引入）                                                                                      | `/(?<num>\d+)/` 提取命名为 `num` 的分组内容 |

---

**其他**

| 符号      | 含义                                                                                                                                     | 示例                              |
|-----------|------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| `|`       | 或运算符，匹配左边或右边的表达式                                                                                                        | `/a|b/` 匹配 `a` 或 `b`           |
| `\p{}`    | 匹配指定 Unicode 属性字符（需配合 `u` 修饰符）                                                                                          | `/\p{L}/u` 匹配任意字母          |
| `\P{}`    | 匹配不符合指定 Unicode 属性的字符                                                                                                       | `/\P{L}/u` 匹配非字母字符         |