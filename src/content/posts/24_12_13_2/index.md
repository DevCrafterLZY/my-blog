---
title: 学习JS (1)
published: 2024-12-13
description: "let和const命令,变量的解构赋值。"
tags: ["JavaScript"]
category: Notes
draft: false
---

### 1. let, var命令

let与var类似，推荐使用let

let与var作用域不同,let是**块作用域**，var是**函数作用域**。

```javascript
{
    let a = 10;
    var b = 1;
}

console.log(a)  // 报错，let的作用域只在{}内
console.log(b)  // var可以在
```
### 2. const命令
const声明一个只读的常量，是**块作用域**。一旦声明，常量的值就不能改变。

所以const一旦声明变量，就必须立即初始化，不能留到以后赋值。

:::tip
const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。
对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。
但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针。
const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。
:::


如果真的想将对象冻结，应该使用`Object.freeze`方法。
```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

### 3. 顶层对象的属性
顶层对象，在浏览器环境指的是`window`对象，在 Node 指的是`global`对象。
```javascript
window.a = 1;
a // 1

a = 2;
window.a // 2
```
ES5 之中，顶层对象的属性与全局变量是等价的。上面代码中，顶层对象的属性赋值与全局变量的赋值，是同一件事。

ES6 为了改变这一点：
- 一方面规定，为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性；
- 另一方面规定，let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

### 4.globalThis对象
JavaScript 语言存在一个顶层对象，它提供全局环境（即全局作用域），所有代码都是在这个环境中运行。但是，顶层对象在各种实现里面是不统一的。

- 浏览器里面，顶层对象是`window`，但 Node 和 Web Worker 没有`window`。
- 浏览器和 Web Worker 里面，`self`也指向顶层对象，但是 Node 没有`self`。
- Node 里面，顶层对象是`global`，但其他环境都不支持。

很难找到一种方法，可以在所有情况下，都取到顶层对象。下面是两种勉强可以使用的方法。
```javascript
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);

// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};
```
:::tip
但任何环境下，`globalThis`都是存在的，都可以从它拿到顶层对象，指向全局环境下的`this`。
:::tip

### 4.变量的解构赋值
#### 4.1.按数组
本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。下面是一些使用嵌套数组进行解构的例子。
```javascript
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```
只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值
```javascript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```

#### 4.2.按对象
对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。
```javascript
let { foo: baz, bar } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined
bar // "bbb"
```
:::tip
按数组，按对象都可设置默认值
默认值生效的条件是，对象的属性值严格等于undefined。
```javascript
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null

let {x, y = 5} = {x: 1};
x // 1
y // 5
```
:::tip

#### 4.3. 用途
##### 1）交换变量的值

```javascript
let x = 1;
let y = 2;

[x, y] = [y, x];
```

上面代码交换变量 `x` 和 `y` 的值，这样的写法不仅简洁，而且易读，语义非常清晰。

---

##### 2）从函数返回多个值

函数只能返回一个值，如果要返回多个值，只能将它们放在数组或对象里返回。有了**解构赋值**，取出这些值就非常方便。

返回一个数组

```javascript
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();
```

返回一个对象

```javascript
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```

---

##### 3）函数参数的定义

解构赋值可以方便地将一组参数与变量名对应起来。

参数是一组有次序的值

```javascript
function f([x, y, z]) { 
  // ... 
}
f([1, 2, 3]);
```

参数是一组无次序的值

```javascript
function f({ x, y, z }) { 
  // ... 
}
f({ z: 3, y: 2, x: 1 });
```

---

##### 4）提取 JSON 数据

解构赋值对提取 JSON 对象中的数据，尤其有用。

```javascript
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 输出: 42, "OK", [867, 5309]
```

上面代码可以快速提取 JSON 数据的值。

---

##### 5）函数参数的默认值

```javascript
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```

指定参数的默认值，就避免了在函数体内部再写 `var foo = config.foo || 'default foo';` 这样的语句。

---

##### 6）遍历 Map 结构

任何部署了 `Iterator` 接口的对象，都可以用 `for...of` 循环遍历。`Map` 结构原生支持 `Iterator` 接口，配合变量的解构赋值，获取键名和键值就非常方便。

```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// 输出: 
// first is hello
// second is world
```

如果只想获取键名，或者只想获取键值，可以写成下面这样：

获取键名：
```javascript
for (let [key] of map) {
  // ...
}
```

获取键值：
```javascript
for (let [, value] of map) {
  // ...
}
```

---

##### 7）输入模块的指定方法

加载模块时，往往需要指定输入哪些方法。解构赋值使得输入语句非常清晰。

```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```