# ES6 中的属性遍历

## 一、`for...in` 循环

`for...in` 循环用于遍历对象的可枚举属性，包括对象自身的属性以及从原型链中继承的属性。

```javascript
const obj = { a: 1, b: 2, c: 3 };

for (const key in obj) {
  if (obj.hasOwnProperty(key)) {
    console.log(`${key}: ${obj[key]}`); // 输出 a: 1, b: 2, c: 3
  }
}
```

## 二、`Object.keys()` 方法

`Object.keys()` 方法返回一个由对象自身的可枚举属性名组成的数组。

```javascript
const obj = { a: 1, b: 2, c: 3 };

const keys = Object.keys(obj);
keys.forEach(key => {
  console.log(`${key}: ${obj[key]}`); // 输出 a: 1, b: 2, c: 3
});
```

## 三、`Object.values()` 方法

`Object.values()` 方法返回一个由对象自身的可枚举属性值组成的数组。

```javascript
const obj = { a: 1, b: 2, c: 3 };

const values = Object.values(obj);
values.forEach(value => {
  console.log(value); // 输出 1, 2, 3
});
```

## 四、`Object.entries()` 方法

`Object.entries()` 方法返回一个由对象自身的可枚举属性的键值对组成的数组。

```javascript
const obj = { a: 1, b: 2, c: 3 };

const entries = Object.entries(obj);
entries.forEach(([key, value]) => {
  console.log(`${key}: ${value}`); // 输出 a: 1, b: 2, c: 3
});
```

## 五、`Object.getOwnPropertyNames()` 方法

`Object.getOwnPropertyNames()` 方法返回一个数组，其包含给定对象中所有自身的属性（包括不可枚举属性，但不包括使用 `symbol` 值作为名称的属性）。

```javascript
const obj = { a: 1, b: 2, c: 3 };
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });

const propertyNames = Object.getOwnPropertyNames(obj);
propertyNames.forEach(name => {
  console.log(`${name}: ${obj[name]}`); // 输出 a: 1, b: 2, c: 3, d: 4
});
```

## 六、`Object.getOwnPropertySymbols()` 方法

`Object.getOwnPropertySymbols()` 方法返回一个包含给定对象所有自身的 Symbol 属性的数组。

```javascript
const sym1 = Symbol('foo');
const sym2 = Symbol('bar');

const obj = {
  [sym1]: 'baz',
  [sym2]: 'qux'
};

const symbols = Object.getOwnPropertySymbols(obj);
symbols.forEach(symbol => {
  console.log(`${symbol.toString()}: ${obj[symbol]}`); // 输出 Symbol(foo): baz, Symbol(bar): qux
});
```

## 七、`Reflect.ownKeys()` 方法

`Reflect.ownKeys()` 方法返回一个由对象自身的所有属性（包括 Symbol 属性和不可枚举属性）组成的数组。

```javascript
const sym1 = Symbol('foo');
const sym2 = Symbol('bar');

const obj = { a: 1, b: 2, c: 3 };
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });
obj[sym1] = 'baz';
obj[sym2] = 'qux';

const ownKeys = Reflect.ownKeys(obj);
ownKeys.forEach(key => {
  console.log(`${key.toString()}: ${obj[key]}`); // 输出 a: 1, b: 2, c: 3, d: 4, Symbol(foo): baz, Symbol(bar): qux
});
```

## 八、遍历数组的其他方法

虽然数组的遍历并不完全属于“属性”遍历，但在很多场景下也是很常用的。以下是一些常见的遍历数组的方法：

### 1. `forEach`

`forEach` 方法对数组的每个元素执行一次提供的函数。该方法不会改变原数组，且无法使用 `break`、`continue` 跳出循环。

```javascript
const array = [1, 2, 3, 4, 5];

array.forEach((value, index, arr) => {
  console.log(value); // 输出 1, 2, 3, 4, 5
});
```

### 2. `map`

`map` 方法创建一个新数组，其结果是对原数组的每个元素调用提供的函数后的返回值。该方法不会改变原数组。

```javascript
const array = [1, 2, 3, 4, 5];

const newArray = array.map(value => value * 2);
console.log(newArray); // 输出 [2, 4, 6, 8, 10]
```

### 3. `filter`

`filter` 方法创建一个新数组，其包含所有通过测试函数的元素。该方法不会改变原数组。

```javascript
const array = [1, 2, 3, 4, 5];

const filteredArray = array.filter(value => value > 2);
console.log(filteredArray); // 输出 [3, 4, 5]
```

### 4. `reduce`

`reduce` 方法对数组的每个元素执行提供的函数（从左到右），将其结果汇总为单个返回值。

```javascript
const array = [1, 2, 3, 4, 5];

const sum = array.reduce((accumulator, currentValue) => accumulator + currentValue, 0);
console.log(sum); // 输出 15
```

### 5. `some`

`some` 方法测试数组中的某些元素是否通过了测试函数。只要有一个元素通过测试，返回值就是 `true`。

```javascript
const array = [1, 2, 3, 4, 5];

const hasValueGreaterThanThree = array.some(value => value > 3);
console.log(hasValueGreaterThanThree); // 输出 true
```

### 6. `every`

`every` 方法测试数组中的所有元素是否通过了测试函数。只有所有元素都通过测试，返回值才是 `true`。

```javascript
const array = [1, 2, 3, 4, 5];

const allValuesGreaterThanZero = array.every(value => value > 0);
console.log(allValuesGreaterThanZero); // 输出 true
```

### 7. `find`

`find` 方法返回数组中第一个通过测试函数的元素值。如果没有元素通过测试，返回 `undefined`。

```javascript
const array = [1, 2, 3, 4, 5];

const foundValue = array.find(value => value > 3);
console.log(foundValue); // 输出 4
```

### 8. `findIndex`

`findIndex` 方法返回数组中第一个通过测试函数的元素的索引。如果没有元素通过测试，返回 `-1`。

```javascript
const array = [1, 2, 3, 4, 5];

const foundIndex = array.findIndex(value => value > 3);
console.log(foundIndex); // 输出 3
```
