# HTTP 标头

HTTP 标头（HTTP Headers）允许客户端和服务器通过 HTTP 请求或响应传递附加信息。

## 请求标头

### 1. `Accept`

指示客户端希望服务器返回的数据类型。

```http
Accept: text/html
Accept: application/json
```

### 2. `Accept-Encoding`

指示客户端支持的内容编码（通常是压缩算法）。

```http
Accept-Encoding: gzip, deflate, br
```

### 3. `Accept-Language`

指示客户端希望的响应语言。

```http
Accept-Language: en-US, en;q=0.9
```

### 4. `Authorization`

包含客户端的认证信息，常用于 API 请求。

```http
Authorization: Basic YWxhZGRpbjpvcGVuc2VzYW1l
Authorization: Bearer token
```

### 5. `Cache-Control`

指示客户端的缓存机制。

```http
Cache-Control: no-cache
Cache-Control: max-age=3600
```

### 6. `Content-Type`

指示请求主体的媒体类型。

```http
Content-Type: application/json
Content-Type: multipart/form-data
```

### 7. `Cookie`

包含客户端存储的 HTTP cookie。

```http
Cookie: sessionId=abc123; lang=en-US
```

## 响应标头

### 1. `Access-Control-Allow-Origin`

指示响应的资源是否可以被特定的源访问，是 CORS 的一部分。

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Origin: https://example.com
```

### 2. `Content-Encoding`

指示返回主体的编码类型（通常是压缩类型）。

```http
Content-Encoding: gzip
Content-Encoding: br
```

### 3. `Content-Length`

指示响应主体的大小（以字节为单位）。

```http
Content-Length: 348
```

### 4. `Content-Type`

指示响应主体的媒体类型。

```http
Content-Type: application/json
Content-Type: text/html; charset=UTF-8
```

### 5. `Set-Cookie`

用于服务器向客户端设置 HTTP cookie。

```http
Set-Cookie: sessionId=abc123; Path=/; Secure; HttpOnly
```

### 6. `Cache-Control`

指示响应的缓存机制。

```http
Cache-Control: no-store
Cache-Control: private, max-age=3600
```

### 7. `Expires`

指示响应过期的日期和时间。

```http
Expires: Wed, 21 Oct 2021 07:28:00 GMT
```

### 通用标头

### 1. `Date`

指示消息生成的日期和时间。

```http
Date: Wed, 21 Oct 2021 07:28:00 GMT
```

### 2. `Connection`

控制是否保持网络连接。

```http
Connection: keep-alive
Connection: close
```

### 3. `Transfer-Encoding`

指示用来安全传输响应主体的数据传输编码。

```http
Transfer-Encoding: chunked
```

## 安全相关标头

### 1. `Strict-Transport-Security (HSTS)`

强制客户端使用 HTTPS 访问。

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

### 2. `Content-Security-Policy (CSP)`

控制用户代理能够为给定页面加载哪些资源。

```http
Content-Security-Policy: default-src 'self'
```

### 3. `X-Content-Type-Options`

防止 MIME 嗅探。

```http
X-Content-Type-Options: nosniff
```

### 4. `X-Frame-Options`

防止点击劫持攻击。

```http
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

### 5. `X-XSS-Protection`

启用跨站脚本过滤。

```http
X-XSS-Protection: 1; mode=block
```

## 代理相关标头

### 1. `Via`

由代理添加，包括正向和反向代理。

```http
Via: 1.1 vegur
```

### 2. `Forwarded`

包含来自代理服务器的信息。

```http
Forwarded: for=192.0.2.60; proto=http; by=203.0.113.43
```

## 其他重要标头

### 1. `ETag`

标识资源版本的唯一字符串，用于缓存验证。

```http
ETag: "33a64df551425fcc55e4d42a148795d9f25f89d4"
```

### 2. `Location`

指示要重定向的 URL。

```http
Location: https://www.example.com
```

### 3. `Server`

包含处理请求的服务器相关信息。

```http
Server: Apache/2.4.1 (Unix)
```

参考

- [MDN HTTP 标头文档](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers)。
