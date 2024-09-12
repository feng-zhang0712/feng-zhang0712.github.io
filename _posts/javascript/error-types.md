# JavaScript 中的错误类型

## 一、Error 实例对象

JavaScript 解析或运行时，一旦发生错误，引擎就会抛出一个错误对象。JavaScript 原生提供 Error 构造函数，所有抛出的错误都是这个构造函数的实例。

JavaScript 语言标准只提到，Error 实例对象必须有 `message` 属性，表示出错时的提示信息。大多数 JavaScript 引擎，对 Error 实例还提供 `name` 和 `stack` 属性，分别表示错误的名称和错误的堆栈，但它们是非标准的，不是每种实现都有。

- `message`：错误提示信息
- `name`：错误名称（非标准属性）
- `stack`：错误的堆栈（非标准属性）

## 二、原生错误类型

### 2.1 语法错误（Syntax Error）

语法错误是指代码中违反了 JavaScript 语法规则的错误。通常在解析代码时（例如代码编译或运行时）被捕获和报告。

```javascript
const x == 10; // SyntaxError: Unexpected token '=='
```

### 2.2 引用错误（ReferenceError）

引用错误是指引用了一个不存在的变量或标识符。

```javascript
console.log(nonExistentVariable); // ReferenceError: nonExistentVariable is not defined
```

### 2.3 范围错误（RangeError）

范围错误是指一个值超出有效范围时发生的错误。例如，数组长度设置为负值，调用一个参数不在有效范围内的函数等。

```javascript
let arr = new Array(-1); // RangeError: Invalid array length
```

### 2.4 类型错误（TypeError）

TypeError 对象是变量或参数不是预期类型时发生的错误。通常是在试图对不支持某操作的值进行操作时引发的。

```javascript
null.f(); // TypeError: Cannot read property 'f' of null
```

### 2.5 URI错误（URIError）

URI 错误是在使用全局 URI 处理函数时，传递了非法的 URI 参数而引发的错误。此种类型的错误主要涉及以下六个方法：

- `encodeURI`
- `decodeURI`
- `decodeURIComponent`
- `encodeURIComponent`
- `escape`
- `unescape`

```javascript
decodeURIComponent('%'); // URIError: URI malformed
```

### 2.6 评估错误（EvalError）

评估错误是在全局`eval()`函数的使用过程中发生的错误。尽管在现代 JavaScript 中已经很少使用 `EvalError`，它仍然是标准的一部分。

```javascript
// 在现代 JavaScript 中很少出现 EvalError
```

### 2.7 运行时错误（Runtime Error）

运行时错误是指代码在执行过程中发生的错误。运行时错误通常是由各种原因引起的，例如引用未定义的变量、调用不存在的方法等。

```javascript
let x;
console.log(x.y); // TypeError: Cannot read property 'y' of undefined
```

## 三、自定义错误

开发者可以创建自定义错误类型，使用 `Error` 对象或其子类来表示特定的错误类型。通过这种方式，可以更好地处理和调试应用程序中的错误。

```javascript
class CustomError extends Error {
  constructor(message) {
    super(message);
    this.name = 'CustomError';
    this.date = new Date();
  }
}

throw new CustomError('This is a custom error');
```

## 四、错误处理

在 JavaScript 中，错误处理主要通过 `try...catch...finally` 语句来实现。该语句允许开发者捕获和处理代码执行过程中发生的错误。

```javascript
try {
  // 可能会抛出错误的代码
  let result = someFunction();
  console.log(result);
} catch (error) {
  // 错误处理代码
  console.error('An error occurred:', error.message);
} finally {
  // 无论是否发生错误，都会执行的代码
  console.log('Execution completed');
}
```

参考

- [错误处理机制](https://wangdoc.com/javascript/features/error)
