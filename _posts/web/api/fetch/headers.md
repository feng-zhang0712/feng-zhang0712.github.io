# Headers

`Headers` 接口是 Fetch API 中用于操作 HTTP 请求和响应头的对象。它提供了一组方法，可以很方便地设置、获取、删除和遍历 HTTP 头部信息。`Headers` 接口在请求（`Request`）和响应（`Response`）对象中都使用。

## 一、创建 `Headers` 对象

你可以通过 `Headers` 构造函数来创建一个新的 `Headers` 对象：

```javascript
const myHeaders = new Headers();
```

你还可以通过传入一个对象、数组或另一个 `Headers` 对象来初始化：

```javascript
// 使用对象字面量初始化
const myHeaders = new Headers({
  'Content-Type': 'application/json',
  'Authorization': 'Bearer token'
});

// 使用二维数组初始化
const myHeaders = new Headers([
  ['Content-Type', 'application/json'],
  ['Authorization', 'Bearer token']
]);

// 使用另一个 Headers 对象初始化
const originalHeaders = new Headers({
  'Content-Type': 'application/json'
});
const myHeaders = new Headers(originalHeaders);
```

## 二、常用方法

### 2.1 `append(name, value)`

添加一个新的头部，如果头部已经存在，则将新的值追加到已有值之后。

```javascript
myHeaders.append('Content-Type', 'application/json');
myHeaders.append('Content-Type', 'text/html');
```

### 2.2 `set(name, value)`

设置一个头部，如果头部已经存在，则覆盖其值。

```javascript
myHeaders.set('Content-Type', 'application/json');
myHeaders.set('Authorization', 'Bearer token');
```

### 2.3 `get(name)`

获取指定头部的第一个值。如果头部不存在，则返回 `null`。

```javascript
console.log(myHeaders.get('Content-Type')); // 'application/json'
```

### 2.4 `has(name)`

判断是否存在指定的头部，返回布尔值。

```javascript
console.log(myHeaders.has('Content-Type')); // true
```

### 2.5 `delete(name)`

删除指定的头部。

```javascript
myHeaders.delete('Authorization');
console.log(myHeaders.has('Authorization')); // false
```

### `entries()`

返回一个包含所有头部键值对的迭代器。

```javascript
for (const [key, value] of myHeaders.entries()) {
  console.log(`${key}: ${value}`);
}
```

### 2.6 `keys()`

返回一个包含所有头部名称（键）的迭代器。

```javascript
for (const key of myHeaders.keys()) {
  console.log(key);
}
```

### 2.7 `values()`

返回一个包含所有头部值的迭代器。

```javascript
for (const value of myHeaders.values()) {
  console.log(value);
}
```

### 2.8 `forEach(callback)`

对 `Headers` 对象中的每个头部执行一次指定的回调函数。

```javascript
myHeaders.forEach((value, key) => {
  console.log(`${key}: ${value}`);
});
```

## 三、示例

以下是如何使用 `Headers` 对象来设置请求头的一个完整示例：

```javascript
const myHeaders = new Headers();
myHeaders.append('Content-Type', 'application/json');
myHeaders.append('Authorization', 'Bearer token');

const requestOptions = {
  method: 'GET',
  headers: myHeaders,
  redirect: 'follow'
};

fetch('https://api.example.com/data', requestOptions)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

## 四、特别注意

- **只读属性**: 有些头部是只读的，不能通过 `Headers` 接口进行修改，例如 `Content-Length`。
- **非法头部**: 使用不合法的 HTTP 头部属性名会导致方法抛出 `TypeError` 异常。
- **Guard 属性**: `Headers` 对象有一个内部的 `guard` 属性，影响可以被操作的内容。可能的值有：`none`、`request`、`request-no-cors`、`response` 和 `immutable`，其中 `immutable` 在 `ServiceWorkers` 中最常用。

## 五、兼容性

可以通过如下方式检测 `Headers` 是否在当前环境中支持：

```javascript
if (window.Headers) {
  console.log('Headers API is supported!');
} else {
  console.log('Headers API is not supported.');
}
```
