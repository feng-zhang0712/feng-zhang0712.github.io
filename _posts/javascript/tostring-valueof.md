# Object.prototype.toString() 和 Object.prototype.valueOf()

## 一、`toString()` 方法

### 1. 定义

`toString()` 方法返回一个表示该对象的字符串。当需要字符串表示对象时，JavaScript 会自动调用此方法。

### 2. 默认行为

默认情况下，`Object.prototype.toString()` 返回一个表示对象的字符串格式 `[object Type]`，其中 `Type` 是对象的类型。

```javascript
const obj = {};
console.log(obj.toString()); // 输出 "[object Object]"

const arr = [];
console.log(arr.toString()); // 输出 ""

const date = new Date();
console.log(date.toString()); // 输出当前日期和时间的字符串表示
```

### 3. 覆写 `toString()`

可以在自定义对象中覆写 `toString()` 方法，以提供更有意义的字符串表示。

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.toString = function() {
  return `Name: ${this.name}, Age: ${this.age}`;
};

const person = new Person('Alice', 30);
console.log(person.toString()); // 输出 "Name: Alice, Age: 30"
```

### 4. 自动调用 `toString()`

`toString()` 方法会在需要字符串表示对象的场合自动调用，例如字符串连接和模板字符串中。

```javascript
const obj = {
  toString: function() {
    return 'Custom Object';
  }
};

console.log('Object is: ' + obj); // 输出 "Object is: Custom Object"
console.log(`Object is: ${obj}`); // 输出 "Object is: Custom Object"
```

## 二、`valueOf()` 方法

### 1. 定义

`valueOf()` 方法返回指定对象的原始值。JavaScript 在需要对象的原始值时会自动调用此方法。

### 2. 默认行为

默认情况下，`Object.prototype.valueOf()` 方法返回对象本身。这通常并不常用，但在某些自定义对象中，可以覆写 `valueOf()` 方法以返回更有意义的值。

```javascript
const obj = {};
console.log(obj.valueOf() === obj); // 输出 true

const date = new Date();
console.log(date.valueOf()); // 输出自 1970-01-01 00:00:00 UTC 以来的毫秒数
```

### 3. 覆写 `valueOf()`

可以在自定义对象中覆写 `valueOf()` 方法，以提供特定的原始值表示。

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.valueOf = function() {
  return this.age;
};

const person = new Person('Alice', 30);
console.log(person.valueOf()); // 输出 30
console.log(person + 10); // 输出 40，因为 person 转换为其 age 值
```

### 4. 自动调用 `valueOf()`

`valueOf()` 方法会在需要对象的原始值的场合自动调用，例如在算术运算和比较运算中。

```javascript
const obj = {
  valueOf: function() {
    return 42;
  }
};

console.log(obj + 8); // 输出 50
console.log(obj > 40); // 输出 true
```

## 三、区别与联系

- `toString()` 返回对象的字符串表示，而 `valueOf()` 返回对象的原始值。
- 当 JavaScript 需要字符串表示对象时，会自动调用 `toString()` 方法；当需要对象的原始值时，会自动调用 `valueOf()` 方法。
- 可以覆写这两个方法，以提供特定对象的自定义表示和行为。

```javascript
function CustomObject(value) {
  this.value = value;
}

CustomObject.prototype.toString = function() {
  return `Value is ${this.value}`;
};

CustomObject.prototype.valueOf = function() {
  return this.value;
};

const customObj = new CustomObject(99);
console.log(customObj.toString()); // 输出 "Value is 99"
console.log(customObj.valueOf()); // 输出 99
console.log(customObj + 1); // 输出 100，因为调用了 valueOf()
console.log(`Custom object: ${customObj}`); // 输出 "Custom object: Value is 99"，因为调用了 toString()
```
