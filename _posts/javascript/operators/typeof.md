# typeof

## 涵义

`typeof` 运算符返回一个字符串，表示操作数的类型。`typeof` 运算符对各种数据类型的返回值如下。

- Undefined 类型返回 `"undefined"`
- Null 类型返回 `"object"`
- Boolean 类型返回 `"boolean"`
- Number 类型返回 `"number"`
- String 类型返回 `"string"`
- BigInt 类型返回 `"bigint"`
- Symbol 类型返回 `"symbol"`
- Function （classes 也是函数）类型返回 `"function"`
- 其他任何对象类型返回 `"object"`

## typeof null

```javascript
// JavaScript 诞生以来便如此
typeof null === "object";
```

在 JavaScript 最初的实现中，JavaScript 中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是 `0`。由于 `null` 代表的是空指针（大多数平台下值为 `0x00`），因此，`null` 的类型标签是 `0`，`typeof null` 也因此返回 `"object"`。

## typeof 的模拟实现

`````javascript
function myTypeOf(value) {
  if (value === null) {
    return "object"; // 特例：null 返回 "object"
  }
  if (typeof value === "undefined") {
    return "undefined"; // 特例：undefined 返回 "undefined"
  }
  return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}
```

## 参考

- [typeof](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof)
