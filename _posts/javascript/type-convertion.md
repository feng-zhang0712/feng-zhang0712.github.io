# Javascript 中的类型转换

## 一、概述

Javascript 是一门动态语言，这意味着，不同类型的变量在进行运算时，会发生类型转换。Javascript 中的类型转换分为强制类型转换和自动类型转换（有时，也称为显示类型转换以及隐式类型转换）。

## 二、强制类型转换

强制类型转换是指使用明确的语法将一种数据类型转换为另一种数据类型。强制类型转换主要是指数值、字符串以及布尔值之间的类型转换。

### 2.1 转换为布尔值

Boolean() 函数可以将任意类型的值转为布尔值。

它的转换规则，除了以下五个值的转换结果为 `false`，其他的值全部为 `true`。

- `''`（空字符串）
- `0`（包含 `-0` 和 `+0`）
- `NaN`
- `null`
- `undefined`

```javascript
Boolean('') // false
Boolean(0) // false
Boolean(NaN) // false
Boolean(null) // false
Boolean(undefined) // false
```

### 2.2 转换为字符串

`String()` 函数可以将任意类型的值转化成字符串，转换规则如下。

#### （1）原始类型值

- 字符串：转换后还是原来的值。
- 数值：转为相应的字符串。
- 布尔值：`true` 转为字符串 `"true"`，`false` 转为字符串 `"false"`。
- `undefined`：转为字符串 `"undefined"`。
- `null`：转为字符串 `"null"`。

```javascript
String('abc') // "abc"
String(123) // "123"
String(true) // "true"
String(undefined) // "undefined"
String(null) // "null"
```

#### （2）对象

`String` 方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。

```javascript
String({a: 1}) // "[object Object]"
String([1, 2, 3]) // "1,2,3"
```

`String` 方法背后的转换规则如下。

1. 先调用对象自身的 `toString` 方法。如果返回原始类型的值，则对该值使用 `String` 函数。
2. 如果 `toString` 方法返回的是对象，再调用原对象的 `valueOf` 方法。如果 `valueOf` 方法返回原始类型的值，则对该值使用 `String` 函数。
3. 如果 `valueOf` 方法返回的是对象，就报错。

### 2.3 转换为数值

`Number()` 函数可以将任意类型的值转化成数值。这其中又分为，参数是原始类型和对象类型。

#### （1）原始类型的值

```javascript

Number(123); // 123

Number('') // 0
Number('123'); // 123
Number('123.5'); // 123.5
Number('123abc'); // NaN，无法解析为数字
Number('   123'); // 123
Number('0x1A'); // 26，解析为十六进制数

Number(true); // 1
Number(false); // 0

Number(null); // 0

Number(undefined); // NaN
```

此外，在讲字符串转为数值是，还可以使用 `parseInt(string, radix)` 方法。`parseInt` 方法接受两个参数，要进行转换的字符串以及期望的基数。

```javascript
parseInt(123.5); // 123

parseInt('123'); // 123
parseInt('123abc'); // 123
parseInt('   123'); // 123
parseInt('0x1A'); // 26，解析为十六进制数
parseInt('10', 2); // 2，解析为二进制数
parseInt('abc'); // NaN，无法解析为数字

parseInt(true); // NaN
parseInt(false); // NaN

parseInt(null); // NaN

parseInt(undefined); // NaN
```

`Number` 函数和 `parseInt` 方法的特点主要有如下几点。

1. 两者都会忽略参数中的空字符串。
2. `parseInt` 方法可以设置转换基数。
3. 如果字符串中含有非数值字符，`parseInt` 会忽略非数值字符，而 `Number` 函数则不会。
4. `Number` 函数可以将布尔值、`null`、`undefined` 和对象转换为数字，而 `parseInt` 一般只用来解析字符串。

#### （2）对象类型

`Number` 方法的参数是对象时，将返回 `NaN`，除非是包含单个数值的数组。

```javascript
Number({a: 1}) // NaN
Number([1, 2, 3]) // NaN
Number([5]) // 5
```

这是因为，当 `Number` 函数的参数是对象时，它会进行如下三个转换步骤。

1. 调用对象自身的 `valueOf` 方法。如果返回原始类型的值，则对该值使用 `Number` 函数。
2. 如果 `valueOf` 方法返回的是对象，则改为调用对象自身的 `toString` 方法。如果 `toString` 方法返回原始类型的值，则对该值使用 `Number` 函数。
3. 如果 `toString` 方法返回的是对象，就报错。

## 二、自动类型转换

自动类型转换通常发生在以下三种情况下：算术运算、比较运算和逻辑运算。

### 2.1 自动转换为布尔值

在条件判断中，非布尔类型的数据会被自动（隐式地）转换为布尔值。

```javascript
if ( !undefined
  && !null
  && !0
  && !NaN
  && !''
) {
  console.log('true');
} // true
```

### 2.2 自动转换为字符串

当一个非字符串类型的数据与字符串进行连接（比如，字符串的加法 `+` 运算时）时，非字符串类型的数据会被自动（隐式地）转换为字符串。

```javascript
'5' + 1 // '51'
'5' + true // "5true"
'5' + false // "5false"
'5' + {} // "5[object Object]"
'5' + [] // "5"
'5' + function (){} // "5function (){}"
'5' + undefined // "5undefined"
'5' + null // "5null"
```

下面这种自动转换很容易出错。

```javascript
const obj = {
  width: '100'
};

obj.width + 20 // "10020"
```

上面代码中，开发者可能期望返回 `120`，但是由于自动转换，实际上返回了一个字符 `10020`。

### 2.3 自动转换为数值

当进行算术运算（除了加法运算符 `+` 有可能把运算子转为字符串）时，非数字类型的数据会被自动（隐式地）转换为数字。

```javascript
'5' - '2' // 3
'5' * '2' // 10
true - 1  // 0
false - 1 // -1
'1' - 1   // 0
'5' * []    // 0
false / '5' // 0
'abc' - 1   // NaN
null + 1 // 1
undefined + 1 // NaN
```

注意：`null` 转为数值时为 `0`，而 `undefined` 转为数值时为 `NaN`。

## 三、特殊情况

### 3.1 `==` 和 `===` 的区别

- `==` 运算符会进行类型转换后再比较
- `===` 运算符不会进行类型转换，要求类型和值都相等

```javascript
console.log(123 == '123'); // 输出 true
console.log(123 === '123'); // 输出 false
```

### 3.2 `+` 运算符的特殊情况

`+` 运算符不仅用于算术运算，还用于字符串连接。在使用时需要注意其行为差异。

```javascript
console.log(1 + '2'); // 输出 "12"，数字被转换为字符串
console.log(1 + 2); // 输出 3，进行算术运算
```

## 四、参考

- 阮一峰，[数据类型的转换](https://wangdoc.com/javascript/features/conversion)
