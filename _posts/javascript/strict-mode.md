# 严格模式

严格模式（Strict Mode）是 ES5 引入的一种 JavaScript 运行模式，它通过抛出更多的错误警告来帮助开发者编写更健壮、更安全的代码。严格模式消除了 JavaScript 中一些不合理、不安全的行为，修复了一些让人困惑的功能，同时让部分代码运行得更快。

## 一、引入严格模式的目的

- 消除 Javascript 语法的一些不合理、不严谨之处，减少一些怪异行为
- 消除代码运行的一些不安全之处，保证代码运行的安全
- 提高编译器效率，增加运行速度

## 二、启用严格模式

严格模式可以在全局范围内或函数范围内启用。要启用严格模式，只需要在脚本或函数的开头加上 `"use strict";` 声明。

```javascript
// 全局范围内启用严格模式
"use strict";
function myFunction() {
  // 函数体代码
}

// 函数范围内启用严格模式
function myFunction() {
  "use strict";
  // 函数体代码
}
```

## 三、严格模式下的行为差异

严格模式引入了许多行为上的差异，以提高代码的安全性和性能。以下是严格模式下的一些主要行为差异。

### 3.1 禁止使用未声明的变量

在严格模式下，使用未声明的变量会抛出 `ReferenceError` 错误。

```javascript
// 非严格模式
x = 10; // 不会抛出错误
console.log(x); // 10

// 严格模式
"use strict";
x = 10; // ReferenceError: x is not defined
```

### 3.2 显式报错

在非严格模式下，一些默认失败的赋值操作会被忽略，而在严格模式下会抛出错误。

#### （1）禁止赋值到只读属性

严格模式下，赋值到只读属性会抛出错误，而在正常模式下，不会抛出错误，只会静默失败。

```javascript
var obj = {};
Object.defineProperty(obj, "prop", { value: 42, writable: false });

// 非严格模式
obj.prop = 9; // 静默失败

// 严格模式
"use strict";
obj.prop = 9; // 报错：赋值给只读属性
```

#### （2）禁止删除不可删除的属性

严格模式下，只有 `configurable` 设置为 `true` 的对象属性，才能被删除，否则会抛出错误。而在正常模式下，不会抛出错误，只会静默失败。

```javascript
// 严格模式
"use strict";
delete obj.name; // TypeError: Cannot delete property 'name' of function Object() { [native code] }

var obj = Object.create(null, {'name': {
　value: 'Tom',
　configurable: true
}});

delete obj.name; // 删除成功
```

### 3.3 重名错误

#### （1）对象不能有重名的属性

正常模式下，如果对象有多个重名属性，最后赋值的那个属性会覆盖前面的值。严格模式下，会抛出 `SyntaxError`。

```javascript
// 非严格模式
var obj = {
  a: 1,
  a: 2
};
console.log(obj.a); // 2

// 严格模式
"use strict";
var obj = {
  a: 1,
  a: 2 // SyntaxError: Duplicate data property in object literal not allowed in strict mode
};
```

#### （2）函数不能有重名的参数

正常模式下，如果函数有多个重名的参数，可以用 `arguments[i]` 读取。严格模式下，会抛出 `SyntaxError`。

```javascript
// 非严格模式
function sum(a, a, c) {
  return a + a + c; // 结果为 (最后一个 a * 2 + c)
}

// 严格模式
"use strict";
function sum(a, a, c) {
  return a + a + c; // SyntaxError: Duplicate parameter name not allowed in this context
}
```

### 3.4 `arguments` 对象的限制

`arguments` 是函数的参数对象，严格模式对它的使用做了限制。

#### （1）不允许对 `arguments` 赋值

严格模式下，不能对 `eval` 和 `arguments` 赋值，也不能使用它们作为变量名。

```javascript
// 非严格模式
var eval = 10;
var arguments = 20;

// 严格模式
"use strict";
var eval = 10; // SyntaxError: Unexpected eval or arguments in strict mode
var arguments = 20; // SyntaxError: Unexpected eval or arguments in strict mode
```

#### （2）`arguments` 不再追踪参数的变化

严格模式下，对 `arguments` 对象的修改不会影响函数参数。

```javascript
// 非严格模式
function foo(a) {
  a = 2;
  console.log(arguments[0]); // 输出 2
}
foo(1);

// 严格模式
"use strict";
function foo(a) {
  a = 2;
  console.log(arguments[0]); // 输出 1
}
foo(1);
```

#### （3）禁止使用 `arguments.callee`

这意味着，你无法在匿名函数内部调用自身了。

```javascript
"use strict";
var f = function() {
  return arguments.callee;
};

f(); // 报错
```

### 3.5 静态绑定

严格模式对动态绑定做了一些限制。某些情况下，只允许静态绑定。也就是说，属性和方法到底归属哪个对象，在编译阶段就确定。这样做有利于编译效率的提高，也使得代码更容易阅读，更少出现意外。

#### （1）禁止使用 `with` 语句

因为 `with` 语句无法在编译时就确定，属性到底归属哪个对象。

```javascript
// 非严格模式
with (Math) {
  var x = cos(2);
}

// 严格模式
"use strict";
with (Math) {
  var x = cos(2); // SyntaxError: Strict mode code may not include a with statement
}
```

#### （2）创设 `eval` 作用域

正常模式下，Javascript 语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：`eval` 作用域。

正常模式下，`eval` 语句的作用域，取决于它处于全局作用域，还是处于函数作用域。严格模式下，`eval` 语句本身就是一个作用域，不再能够生成全局变量了，它所生成的变量只能用于 `eval` 内部。

```javascript
  "use strict";
　var x = 2;
　console.info(eval("var x = 5; x")); // 5
　console.info(x); // 2
```

### 3.6 增强的安全措施

#### （1）禁止 `this` 关键字指向全局对象

```javascript
  function f(){
　　return !this;
　}
　// 返回false，因为"this"指向全局对象，"!this"就是false

　function f(){
　　"use strict";
　　return !this;
　}
　// 返回true，因为严格模式下，this的值为undefined，所以"!this"为true。
```

因此，使用构造函数时，如果忘了加 `new`，`this` 不再指向全局对象，而是报错。

```javascript
　function f(){
　　"use strict";
　　this.a = 1;
　};
　f();// 报错，this未定义
```

#### （2）禁止在函数内部遍历调用栈

```javascript
　function f1(){
　　"use strict";
　　f1.caller; // 报错
　　f1.arguments; // 报错
　}
　f1();
```

### 3.7 禁止使用八进制字面量

严格模式下，八进制字面量（以 0 开头的数字）被禁止。

```javascript
// 非严格模式
var num = 010; // 八进制 8
console.log(num); // 8

// 严格模式
"use strict";
var num = 010; // SyntaxError: Octal literals are not allowed in strict mode.
```

参考：

- [Javascript 严格模式详解](https://www.ruanyifeng.com/blog/2013/01/javascript_strict_mode.html)
