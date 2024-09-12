# Response 对象

`Response` 对象是 Fetch API 的关键组件之一，用于表示 HTTP 响应。它包含了响应的状态码、头部信息以及响应体数据。

## 一、创建 `Response` 对象

你可以通过 `Response` 构造函数创建一个新的 `Response` 对象，通常在 Service Workers 中使用：

```javascript
const response = new Response('Hello, world!', {
  status: 200,
  statusText: 'OK',
  headers: {
    'Content-Type': 'text/plain'
  }
});
```

## 二、属性

### 2.1 `Response.ok`

一个布尔值，表示响应的状态码是否在 200-299 范围内。

```javascript
console.log(response.ok); // true
```

### 2.2 `Response.status`

响应的 HTTP 状态码，是一个整数。

```javascript
console.log(response.status); // 200
```

### 2.3 `Response.statusText`

响应的状态消息，是一个字符串。

```javascript
console.log(response.statusText); // 'OK'
```

### 2.4 `Response.redirected`

一个布尔值，表明该响应是否为一个重定向的请求的结果。

```javascript
console.log(response.redirected); // false
```

### 2.5 `Response.type`

响应的类型，是一个字符串，可以是以下值之一：

- `basic`: 普通，同源资源的请求。
- `cors`: 跨域资源的请求。
- `error`: 网络错误。
- `opaque`: 非同源请求，不透明响应。
- `opaqueredirect`: 跨域重定向，不透明响应。

```javascript
console.log(response.type); // 'basic'
```

### 2.6 `Response.url`

响应的 URL，是一个字符串。

```javascript
console.log(response.url); // 'https://api.example.com/data'
```

### 2.7 `Response.headers`

`Headers` 对象，包含响应的 HTTP 头部信息。

```javascript
console.log(response.headers.get('Content-Type')); // 'text/plain'
```

### 2.8 `Response.body`

表示响应体的 `ReadableStream` 对象。如果没有响应体，则为 `null`。

```javascript
console.log(response.body); // ReadableStream
```

## 三、静态方法

### 3.1 `Response.error()`

创建一个表示网络错误的 `Response` 对象。

```javascript
const errorResponse = Response.error();
console.log(errorResponse.type); // 'error'
```

### 3.2 `Response.redirect(url, status)`

创建一个表示重定向的 `Response` 对象。`status` 必须是 300-399 范围内的状态码。

```javascript
const redirectResponse = Response.redirect('https://example.com', 302);
console.log(redirectResponse.status); // 302
console.log(redirectResponse.headers.get('Location')); // 'https://example.com'
```

## 四、实例方法

### 4.1 `clone()`

创建一个响应的副本。注意，响应体只能被读取一次，因此在读取响应体之前创建副本是必要的。

```javascript
const responseCopy = response.clone();
```

### 4.2 `arrayBuffer()`

读取响应体并返回一个解析过的 `ArrayBuffer` 对象。

```javascript
response.arrayBuffer().then(buffer => {
  console.log(buffer);
});
```

### 4.3 `blob()`

读取响应体并返回一个解析过的 `Blob` 对象。

```javascript
response.blob().then(blob => {
  console.log(blob);
});
```

### 4.4 `formData()`

读取响应体并返回一个解析过的 `FormData` 对象。

```javascript
response.formData().then(formData => {
  console.log(formData);
});
```

### 4.5 `json()`

读取响应体并返回解析过的 JSON 对象。

```javascript
response.json().then(data => {
  console.log(data);
});
```

### 4.6 `text()`

读取响应体并返回解析过的文本。

```javascript
response.text().then(text => {
  console.log(text);
});
```
