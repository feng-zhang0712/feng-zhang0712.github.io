# this 绑定

## 一、`this` 绑定规则

### 1.1 默认绑定

在全局上下文中或普通函数调用时，`this` 默认绑定到全局对象（浏览器中是 `window`，Node.js 中是 `global`）。

```javascript
function foo() {
  console.log(this);
}

foo(); // 在浏览器中输出 window 对象，在 Node.js 中输出 global 对象
```

### 1.2 显式绑定

通过 `call`、`apply` 或 `bind` 方法，可以显式指定 `this` 的绑定对象。

```javascript
function foo() {
  console.log(this.name);
}

const obj = { name: 'Bob' };

foo.call(obj); // 输出 'Bob'
foo.apply(obj); // 输出 'Bob'

const boundFoo = foo.bind(obj);
boundFoo(); // 输出 'Bob'
```

### 1.3 隐式绑定

当函数作为对象的方法调用时，`this` 绑定到该对象。

```javascript
const obj = {
  name: 'Alice',
  greet: function() {
    console.log(this.name);
  }
};

obj.greet(); // 输出 'Alice'
```

### 1.4 new 绑定

使用 `new` 关键字调用构造函数时，`this` 绑定到新创建的对象。

```javascript
function Person(name) {
  this.name = name;
}

const person = new Person('Carol');
console.log(person.name); // 输出 'Carol'
```

### 1.5 箭头函数绑定

箭头函数不会创建自己的 `this`，它会继承自外围上下文的 `this` 绑定。

```javascript
const obj = {
  name: 'Dave',
  greet: () => {
    console.log(this.name);
  }
};

obj.greet(); // 在浏览器中输出 undefined，在严格模式下输出 undefined
```

在上述示例中，由于箭头函数的 `this` 继承自外围上下文的 `this`，这里是全局对象，因此 `this.name` 结果为 `undefined`。

## 二、不同绑定规则的优先级

- **new 绑定**：最高优先级。
- **显式绑定（call、apply、bind）**：次高优先级。
- **隐式绑定**：再次之。
- **默认绑定**：最低优先级。

当多种绑定规则冲突时，优先级高的规则会生效。

## 三、`this` 在不同上下文中的绑定规则

### 3.1 全局上下文

在全局范围内，`this` 绑定到全局对象。

```javascript
console.log(this); // 在浏览器中输出 window 对象，在 Node.js 中输出 global 对象
```

### 3.2 函数上下文

函数中的 `this` 取决于函数调用方式（普通函数调用、作为对象的方法调用、显式绑定等）。

```javascript
function foo() {
  console.log(this);
}

foo(); // 默认绑定，全局对象
const obj = { foo };
obj.foo(); // 隐式绑定，obj 对象
foo.call(obj); // 显式绑定，obj 对象
new foo(); // new 绑定，新创建的对象
```

### 3.3 回调函数

回调函数中的 `this` 取决于回调函数的调用方式。

```javascript
const obj = {
  name: 'Eve',
  greet: function(callback) {
    callback();
  }
};

function sayName() {
  console.log(this.name);
}

obj.greet(sayName); // 默认绑定，全局对象
obj.greet(sayName.bind(obj)); // 显式绑定，obj 对象
```

### 3.4 箭头函数

箭头函数中的 `this` 继承自其定义时的外围上下文。

```javascript
const obj = {
  name: 'Frank',
  greet: function() {
    const innerGreet = () => {
      console.log(this.name);
    };
    innerGreet();
  }
};

obj.greet(); // 输出 'Frank'
```

### 3.5 构造函数

在构造函数中，`this` 绑定到新创建的对象。

```javascript
function Animal(type) {
  this.type = type;
}

const dog = new Animal('dog');
console.log(dog.type); // 输出 'dog'
```

### 3.6 类

在 ES6 类中，`this` 的行为与构造函数类似。

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(this.name);
  }
}

const person = new Person('Grace');
person.greet(); // 输出 'Grace'
```
