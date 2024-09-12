# Request 对象

`Request` 对象是 Fetch API 的关键组件之一，用于表示资源请求。通过 `Request` 对象，你可以定义请求的各种配置，包括方法、头部、主体等。

## 一、创建 `Request` 对象

可以通过 `Request` 构造函数创建一个新的 `Request` 对象：

```javascript
const request = new Request('https://api.example.com/data', {
  method: 'GET',
  headers: new Headers({
    'Content-Type': 'application/json'
  }),
  mode: 'cors',
  cache: 'default'
});
```

## 二、属性

### 2.1 `Request.method`

请求方法（例如 `GET`, `POST`, `PUT`, `DELETE` 等）。它是一个字符串，默认值为 `GET`。

```javascript
console.log(request.method); // 'GET'
```

### 2.2 `Request.url`

请求的 URL，是一个字符串。

```javascript
console.log(request.url); // 'https://api.example.com/data'
```

### 2.3 `Request.headers`

`Headers` 对象，包含请求的 HTTP 头部信息。

```javascript
console.log(request.headers.get('Content-Type')); // 'application/json'
```

### 2.4 `Request.mode`

`mode` 属性包含请求的模式，用于确定跨域请求是否能得到有效的响应。

- `cors`: 允许跨域请求。
- `no-cors`: 跨域请求，但限制只能使用一部分 HTTP 头部信息。
- `same-origin`: 只允许同源请求。
- `navigate`: 用于导航请求。

```javascript
console.log(request.mode); // 'cors'
```

### 2.5 `Request.credentials`

`credentials` 属性用于表示用户代理是否应该在跨域请求的情况下从其他域发送 cookies。这与 XHR 的 withCredentials 标志相似，不同的是有三个可选值（后者是两个）：

- `omit`: 不发送 cookies。
- `same-origin`: 同源请求发送 cookies。
- `include`: 不论是不是跨域请求，总是发送请求资源域在本地的 cookies、authentication 等验证信息。

```javascript
console.log(request.credentials); // 'same-origin'
```

### 2.6 `Request.cache`

请求的缓存模式，可以是以下几种之一：

- `default`：浏览器自动从 HTTP 缓存中寻找匹配的请求，这包括：
  - 如果缓存匹配上并且有效, 它将直接从缓存中返回资源。
  - 如果缓存匹配上但已经过期，浏览器将会使用传统（ conditional request ）的请求方式去访问远程服务器。如果服务器端显示资源没有改动，它将从缓存中返回资源。否则，如果服务器显示资源变动，那么重新从服务器下载资源更新缓存。
  - 如果缓存没有匹配，浏览器将会以普通方式请求，并且更新已经下载的资源缓存。
- `no-store`：浏览器直接从远程服务器获取资源，不查看缓存，并且不会使用下载的资源更新缓存。
- `reload`：浏览器直接从远程服务器获取资源，不查看缓存，然后使用下载的资源更新缓存。
- `no-cache`：浏览器在其 HTTP 缓存中寻找匹配的请求。
  - 如果有匹配，无论是新的还是陈旧的，浏览器都会向远程服务器发出条件请求。如果服务器指示资源没有更改，则将从缓存中返回该资源。否则，将从服务器下载资源并更新缓存
  - 如果没有匹配，浏览器将发出正常请求，并使用下载的资源更新缓存。
- `force-cache`：浏览器在其 HTTP 缓存中寻找匹配的请求。
  - 如果有匹配项，不管是新匹配项还是旧匹配项，都将从缓存中返回。
  - 如果没有匹配，浏览器将发出正常请求，并使用下载的资源更新缓存。
- `only-if-cached`

```javascript
console.log(request.cache); // 'default'
```

### 2.7 `Request.redirect`

请求的重定向模式，可以是以下几种之一：

- `follow`: 自动遵循重定向。
- `error`: 如果遇到重定向，则抛出一个错误。
- `manual`: 手动处理重定向。

```javascript
console.log(request.redirect); // 'follow'
```

### 2.8 `Request.referrer`

请求的 referrer 信息，是一个字符串，默认值为 `about:client`。

```javascript
console.log(request.referrer); // 'about:client'
```

### 2.9 `Request.referrerPolicy`

请求的 referrerPolicy，可以是以下几种之一：

- `no-referrer`
- `no-referrer-when-downgrade`
- `origin`
- `origin-when-cross-origin`
- `same-origin`
- `strict-origin`
- `strict-origin-when-cross-origin`
- `unsafe-url`

```javascript
console.log(request.referrerPolicy); // ''
```

### 2.10 `Request.integrity`

请求的子资源完整性值（Subresource Integrity），是一个字符串，用于确保资源在传输中没有被篡改。

```javascript
console.log(request.integrity); // ''
```

### 2.11 `Request.keepalive`

一个布尔值，表示请求在页面卸载时是否仍然有效。

```javascript
console.log(request.keepalive); // false
```

### 2.12 `Request.signal`

一个 `AbortSignal` 对象，表示请求的信号，用于中止请求。

```javascript
const controller = new AbortController();
const signal = controller.signal;

const request = new Request('https://api.example.com/data', { signal });
controller.abort();
```

## 三、方法

### 3.1 `clone()`

创建一个请求的副本。注意，请求体只能被读取一次，因此在读取请求体之前创建副本是必要的。

```javascript
const requestCopy = request.clone();
```

### 3.2 `arrayBuffer()`

读取请求体并返回一个解析过的 `ArrayBuffer` 对象。

```javascript
request.arrayBuffer().then(buffer => {
  console.log(buffer);
});
```

### 3.3 `blob()`

读取请求体并返回一个解析过的 `Blob` 对象。

```javascript
request.blob().then(blob => {
  console.log(blob);
});
```

### 3.4 `formData()`

读取请求体并返回一个解析过的 `FormData` 对象。

```javascript
request.formData().then(formData => {
  console.log(formData);
});
```

### 3.5 `json()`

读取请求体并返回解析过的 JSON 对象。

```javascript
request.json().then(json => {
  console.log(json);
});
```

### 3.6 `text()`

读取请求体并返回解析过的文本。

```javascript
request.text().then(text => {
  console.log(text);
});
```
