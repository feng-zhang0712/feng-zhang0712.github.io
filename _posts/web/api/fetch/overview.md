# Fetch API

## 一、概述

Fetch API 提供了一个 JavaScript 接口，用于访问和操纵 HTTP 请求和响应。它引入了一个全局的 `fetch()` 方法，用于跨网络异步获取资源。这一功能以前主要通过 `XMLHttpRequest` 实现。Fetch 提供了更理想的替代方案，适用于 Service Workers 和其他现代技术。

### 1.1 与 `jQuery.ajax()` 的不同

- 当接收到一个错误的 HTTP 状态码时，`fetch()` 返回的 Promise 不会被标记为 reject，而是 resolve 状态（若状态码不在 200-299 范围内，`ok` 属性为 false）。只有在网络故障或请求被阻止时，Promise 才会被标记为 reject。
- 默认情况下，`fetch` 不会发送跨域 cookie，除非使用 `credentials` 选项。

## 二、数据请求

### 2.1 使用 init 配置对象

`fetch()` 接受一个可选的配置对象（init 对象），可用于控制请求的不同配置。

```javascript
const myHeaders = new Headers();
myHeaders.append('Content-Type', 'application/json');

const requestOptions = {
  method: 'GET',
  headers: myHeaders,
  redirect: 'follow'
};

fetch('https://api.example.com/data', requestOptions)
  .then(response => response.json())
  .then(result => console.log(result))
  .catch(error => console.error('Error:', error));
```

### 2.2 发送带凭据的请求

要让浏览器发送包含凭据的请求，可以使用 `credentials` 选项：

- `credentials: 'include'`: 跨域请求也会发送凭据。
- `credentials: 'same-origin'`: 只在同源请求时发送凭据。
- `credentials: 'omit'`: 不发送凭据。

### 2.3 上传数据

可以使用 `FormData`、`input` 元素以及 `fetch()` 来上传文件或 JSON 数据。

```javascript
// 上传 JSON 数据
const data = { username: 'example' };

fetch('https://api.example.com/data', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(data)
})
  .then(response => response.json())
  .then(data => console.log('Success:', data))
  .catch(error => console.error('Error:', error));
```

以下是一个使用 `FormData` 上传文件的示例。

```html
<input type="file" id="fileInput" multiple />

<script>
  const input = document.getElementById('fileInput');
  input.addEventListener('change', () => {
    const formData = new FormData();
    for (const file of input.files) {
      formData.append('files[]', file);
    }

    fetch('https://api.example.com/upload', {
      method: 'POST',
      body: formData
    })
      .then(response => response.json())
      .then(data => console.log('Success:', data))
      .catch(error => console.error('Error:', error));
  });
</script>
```

### 2.4 自定义请求对象

可以使用 `Request` 构造函数创建一个 Request 对象，然后作为参数传给 `fetch`。`Request` 和 `fetch` 接受相同的参数。可以使用 `clone()` 方法创建请求或响应的拷贝。

```javascript
const myRequest = new Request('https://api.example.com/data', {
  method: 'GET',
  headers: new Headers({
    'Content-Type': 'application/json'
  })
});

fetch(myRequest)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

### 2.5 使用 Headers 对象

`Headers` 对象用于操作 HTTP 头部信息，可以通过 `Headers` 构造函数创建。Headers 对象的方法会抛出 `TypeError` 异常，如果使用了非法的 HTTP 头部属性名或不可写的属性。

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

## 三、请求响应

### 3.1 Response 对象

如果遇到网络故障或服务端的 CORS 配置错误，`fetch()` 的 promise 会被 reject，并带上一个 `TypeError` 对象。判断是否成功需要检查 `Response.ok` 属性。`Response` 实例在 `fetch()` 处理完 promise 后返回。常见的 `Response` 属性有：

- `Response.ok`
- `Response.status`
- `Response.statusText`

可以使用 `Response()` 构造方法创建一个 Response 实例，但通常只在 Service Workers 中使用。

### 3.2 Body

请求或响应都可以包含 body 对象，支持以下类型：

- `ArrayBuffer`
- `ArrayBufferView`
- `Blob/File`
- `string`
- `URLSearchParams`
- `FormData`

定义了以下方法以获取 body 内容，这些方法返回一个解析后的 Promise：

- `Request.arrayBuffer()` / `Response.arrayBuffer()`
- `Request.blob()` / `Response.blob()`
- `Request.formData()` / `Response.formData()`
- `Request.json()` / `Response.json()`
- `Request.text()` / `Response.text()`

## 四、特性检测

可以通过检测 `Headers`, `Request`, `Response` 或 `fetch()` 是否在 Window 或 Worker 域中判断 Fetch API 的支持情况。

```javascript
if (window.fetch) {
  console.log('Fetch API is supported!');
} else {
  console.log('Fetch API is not supported.');
}
```

参考

- [Fetch API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)
- [ECMAScript Language Specification # sec-function.prototype.bind](https://tc39.es/ecma262/#sec-function.prototype.bind)