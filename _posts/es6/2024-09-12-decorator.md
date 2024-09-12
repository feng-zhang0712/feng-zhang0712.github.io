---
layout: post
title: ES6 - Decorator
categories: frontend
tags:
  - React
  - es6
---

ES6 中的装饰器（Decorator）是一种用于修改类和类方法行为的语法糖。装饰器提供了一种方便的方式来添加注释或元数据，或者在实际定义时修改类和类方法。需要注意的是，尽管装饰器的提案已经达到第2阶段（Stage 2），但它们尚未成为正式的 ECMAScript 标准。

## 一、装饰器的定义和基本用法

### 1. 定义

装饰器是一个函数，用于修改类的行为。它可以应用于类、类的方法、访问器（getter/setter）、属性、以及方法参数。

### 2. 使用场景

装饰器常用于日志记录、访问控制、性能监控、依赖注入等场景。

### 3. 基本语法

装饰器通过在类或类成员之前加 `@` 标志来调用。

```javascript
function readonly(target, key, descriptor) {
  descriptor.writable = false;
  return descriptor;
}

class Person {
  constructor(name) {
    this.name = name;
  }

  @readonly
  getName() {
    return this.name;
  }
}

const person = new Person('Alice');
console.log(person.getName()); // 输出 "Alice"
person.getName = function() { return 'Bob'; }; // 抛出错误，因为 getName 是只读的
```

## 二、装饰器的参数

装饰器函数通常接受以下参数：

- `target`：装饰的目标（类的原型对象或类的构造函数）
- `key`：装饰的属性名称
- `descriptor`：属性描述符

```javascript
function log(target, key, descriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args) {
    console.log(`Calling ${key} with arguments: ${args}`);
    return originalMethod.apply(this, args);
  };
  return descriptor;
}

class Calculator {
  @log
  add(a, b) {
    return a + b;
  }
}

const calculator = new Calculator();
console.log(calculator.add(2, 3)); // 输出 "Calling add with arguments: 2,3" 和 5
```

## 三、装饰器的类型

### 1. 类装饰器

类装饰器用于类的定义，可以修改或替换类的构造函数。

```javascript
function sealed(constructor) {
  Object.seal(constructor);
  Object.seal(constructor.prototype);
}

@sealed
class Person {
  constructor(name) {
    this.name = name;
  }
}
```

### 2. 方法装饰器

方法装饰器用于类的方法，可以修改方法的属性描述符。

```javascript
function enumerable(value) {
  return function(target, key, descriptor) {
    descriptor.enumerable = value;
  };
}

class Person {
  constructor(name) {
    this.name = name;
  }

  @enumerable(false)
  getName() {
    return this.name;
  }
}
```

### 3. 访问器装饰器

访问器装饰器用于类的访问器（getter/setter）。

```javascript
function configurable(value) {
  return function(target, key, descriptor) {
    descriptor.configurable = value;
  };
}

class Person {
  constructor(name) {
    this._name = name;
  }

  @configurable(false)
  get name() {
    return this._name;
  }

  set name(newName) {
    this._name = newName;
  }
}
```

### 4. 属性装饰器

属性装饰器用于类的属性。

```javascript
function readonly(target, key, descriptor) {
  descriptor.writable = false;
  return descriptor;
}

class Person {
  @readonly
  name = 'Alice';
}
```

### 5. 参数装饰器

参数装饰器用于类方法的参数。

```javascript
function logParameter(target, key, index) {
  const metadataKey = `log_${key}_parameters`;
  if (Array.isArray(target[metadataKey])) {
    target[metadataKey].push(index);
  } else {
    target[metadataKey] = [index];
  }
}

class Person {
  greet(@logParameter message) {
    console.log(message);
  }
}
```

## 四、装饰器的实际使用场景

### 1. 日志记录

使用装饰器记录方法调用的日志。

```javascript
function log(target, key, descriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args) {
    console.log(`Calling ${key} with arguments: ${args}`);
    return originalMethod.apply(this, args);
  };
  return descriptor;
}

class Person {
  @log
  sayHello(name) {
    return `Hello, ${name}`;
  }
}

const person = new Person();
console.log(person.sayHello('Alice')); // 输出日志并返回 "Hello, Alice"
```

### 2. 权限控制

使用装饰器检查用户权限。

```javascript
function checkPermission(role) {
  return function(target, key, descriptor) {
    const originalMethod = descriptor.value;
    descriptor.value = function(...args) {
      if (this.userRole !== role) {
        throw new Error('Permission denied');
      }
      return originalMethod.apply(this, args);
    };
    return descriptor;
  };
}

class User {
  constructor(role) {
    this.userRole = role;
  }

  @checkPermission('admin')
  deleteUser() {
    console.log('User deleted');
  }
}

const admin = new User('admin');
admin.deleteUser(); // 输出 "User deleted"

const guest = new User('guest');
guest.deleteUser(); // 抛出 "Permission denied"
```

### 3. 性能监控

使用装饰器监控方法的执行时间。

```javascript
function time(target, key, descriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args) {
    const start = performance.now();
    const result = originalMethod.apply(this, args);
    const end = performance.now();
    console.log(`${key} took ${(end - start).toFixed(2)} ms`);
    return result;
  };
  return descriptor;
}

class Calculator {
  @time
  add(a, b) {
    return a + b;
  }
}

const calculator = new Calculator();
calculator.add(2, 3); // 输出执行时间
```

## 五、注意事项

1. **装饰器的执行顺序**：装饰器从下到上、从右到左执行。
2. **兼容性**：装饰器目前是实验特性，需要 Babel 或 TypeScript 等编译器的支持。
