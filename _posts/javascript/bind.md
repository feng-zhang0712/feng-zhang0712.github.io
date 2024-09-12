# Function.prototype.bind()

## 一、概述

`bind()` 方法创建一个新函数，当调用该新函数时，它会调用原始函数并将其 `this` 关键字设置为给定的值。还可以传入一系列指定的参数，这些参数会插入到调用新函数时传入的参数的前面。

## 语法

```javascript
function.bind(thisArg, [arg1[, arg2[, ...]]])
```

### 参数

- **`thisArg`**: 调用绑定函数时，作为 `this` 参数传入目标函数 `func` 的值。如果函数不在严格模式下，`null` 和 `undefined` 会被替换为全局对象，并且原始值会被转换为对象。如果使用 `new` 运算符构造绑定函数，则忽略该值。
- **`arg1, ..., argN`**: 在调用 `func` 时，插入到传入绑定函数的参数前的参数。

### 返回值

使用指定的 `this` 值和初始参数（如果提供）创建的给定函数的副本。

## 二、描述

`bind()` 函数创建一个新的绑定函数。调用绑定函数通常会执行其所包装的函数（目标函数）。绑定函数将绑定时传入的参数（包括 `this` 的值和前几个参数）提前存储为其内部状态。

### 特点

- **绑定函数可以进一步绑定**: 通过调用 `boundFn.bind(thisArg, /* more args */)`，可以创建另一个绑定函数 `boundFn2`。新绑定的 `thisArg` 值会被忽略，因为 `boundFn2` 的目标函数是 `boundFn`，而 `boundFn` 已经有一个绑定的 `this` 值了。
- **与构造函数一起使用**: 如果目标函数是可构造的，绑定函数也可以使用 `new` 运算符进行构造。前置的参数会像通常一样传递给目标函数，而提供的 `this` 值会被忽略。
- **`instanceof` 操作符支持**: 当将绑定函数用作 `instanceof` 运算符右操作数时，`instanceof` 会访问绑定函数内部存储的目标函数，并读取其 `prototype` 属性。
- **属性继承**: 绑定函数继承目标函数的原型链，但不会继承目标函数的其他自有属性（例如，如果目标函数是一个类，则不会继承其静态属性）。

### 绑定函数的属性

- **`length`**: 目标函数的 `length` 减去被绑定的参数个数（不包括 `thisArg` 参数），最小值为 `0`。
- **`name`**: 目标函数的 `name` 前加上 "bound " 前缀。

## 三、示例

### 创建绑定函数

```javascript
const module = {
  x: 42,
  getX: function() {
    return this.x;
  }
};

const unboundGetX = module.getX;
console.log(unboundGetX()); // undefined

const boundGetX = unboundGetX.bind(module);
console.log(boundGetX()); // 42
```

### 偏函数

`bind()` 的另一个简单用法是创建一个具有预设初始参数的函数。

```javascript
function list() {
  return Array.prototype.slice.call(arguments);
}

const leadingThirtysevenList = list.bind(null, 37);
console.log(leadingThirtysevenList()); // [37]
console.log(leadingThirtysevenList(1, 2, 3)); // [37, 1, 2, 3]
```

### 配合 `setTimeout()`

```javascript
function LateBloomer() {
  this.petalCount = Math.floor(Math.random() * 12) + 1;
}

// 显示定时开花后花瓣数量的方法
LateBloomer.prototype.bloom = function() {
  window.setTimeout(this.declare.bind(this), 1000);
};

LateBloomer.prototype.declare = function() {
  console.log('I am a beautiful flower with ' + this.petalCount + ' petals!');
};

const flower = new LateBloomer();
flower.bloom(); // 一秒钟后, 调用 declare 方法
```

### 作为构造函数使用的绑定函数

绑定函数可以自动适用于与 `new` 运算符一起使用，以用于构造目标函数创建的新实例。

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function() {
  return this.x + ',' + this.y;
};

const p = new Point(1, 2);
p.toString(); // '1,2'

const YAxisPoint = Point.bind(null, 0);
const emptyObj = new YAxisPoint(5);

console.log(emptyObj.toString()); // '0,5'
console.log(emptyObj instanceof Point); // true
console.log(emptyObj instanceof YAxisPoint); // true
console.log(YAxisPoint.prototype === Point.prototype); // true
```

### 绑定类

在类上使用 `bind()` 保留大部分类的语义，只是当前类的所有静态自有属性会丢失。

### 将方法转换为实用函数

```javascript
const slice = Function.prototype.call.bind(Array.prototype.slice);

function list() {
  return slice(arguments);
}

console.log(list(1, 2, 3)); // [1, 2, 3]
```

参考：

- [Function.prototype.bind()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [ECMAScript Language Specification # sec-function.prototype.bind](https://tc39.es/ecma262/#sec-function.prototype.bind)
