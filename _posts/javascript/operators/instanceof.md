# instanceof

## 涵义

`instanceof` 运算符用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链上。其返回值是一个布尔值。可以通过 `Symbol.hasInstance` 来自定义该运算符的行为。

## instanceof 的模拟实现

要实现 instanceof 运算符的功能，我们需要检查一个对象的原型链中是否包含某个构造函数的 `prototype` 属性。具体来说，要执行以下步骤：

1. 验证左侧参数。如果 `obj` 是 `null` 或 `undefined`，抛出 `TypeError`，因为这些值没有原型链；如果 `obj` 不是对象或函数（即基本类型，如数字、字符串），返回 `false`，因为基本类型不是任何构造函数的实例。
2. 验证右侧参数。如果 `constructor` 不是函数，抛出 `TypeError`，因为标准 `instanceof` 要求右侧是可调用对象；获取 `constructor.prototype`，并确保其为对象且非 `null`，否则抛出 `TypeError`。
3. 遍历原型链。
    - 从 `obj` 开始，使用 `Object.getPrototypeOf()` 获取其原型。
    - 比较当前原型与 `constructor.prototype` 是否相等。
    - 如果相等，返回 `true`；否则，继续获取下一个原型，直到链尾（`null`）。
    - 如果未找到匹配，返回 `false`。

```javascript
function myInstanceOf(obj, constructor) {
  if (obj == null) {
    throw new TypeError("Cannot use 'in' operator to search for 'constructor' in " + obj);
  }
  if (typeof constructor !== 'function') {
    throw new TypeError("Right-hand side of 'instanceof' must be a function");
  }

  if (typeof obj !== 'object' && typeof obj !== 'function') {
    return false;
  }

  let proto = Object.getPrototypeOf(obj);
  if (proto === null) {
    return false;
  }

  const prototype = constructor.prototype;
  if (prototype === null || typeof prototype !== 'object') {
    throw new TypeError("Function has non-object prototype in instanceof check");
  }

  while (proto) {
    if (proto === prototype) {
      return true;
    }
    proto = Object.getPrototypeOf(proto);
  }

  return false;
}
```

上面的实现中，没有考虑对象实现了自定义的 `[Symbol.hasInstance]` 方法的情况，因为这超出了标准 `instanceof` 的默认行为。

## 参考

- [Grok](https://grok.com/share/bGVnYWN5_da811099-d752-4c6a-8b7d-4eccf3f627d8)
