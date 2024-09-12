# HTTP 重定向

## 一、概念

HTTP 重定向是指当客户端（通常是浏览器）请求一个 URL，服务器指示客户端转到另一个 URL 的过程。重定向在 Web 开发中非常常见，用于多种场景，如页面迁移、域名更改、SEO 优化等。HTTP 协议通过状态码和头部字段来实现重定向。

## 二、HTTP 重定向的原理

在 HTTP 中，重定向由服务器触发，服务器会对请求发送一个特殊的重定向响应。重定向响应中包含以 3 开头的状态码，以及一个 `Location` 头字段，这个字段包含了要重定向到的 URL 地址。

当浏览器接收到重定向响应时，会立即加载 `Location` 头字段中提供的新的 URL 地址。除了一个双向的性能损耗之外，用户几乎感知不到重定向的发生。

重定向的过程可以用下边的图示表示。

![HTTP 重定向过程](https://developer.mozilla.org/en-US/docs/Web/HTTP/Redirections/httpredirect.svg)
*图片来自 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Redirections)*

## 三、HTTP 重定向的种类

HTTP 重定向主要包括以下几种类型，分别由不同的状态码表示：

### 3.1 300 Multiple Choices

状态码 `300` 表示，客户端请求一个实际指向多个资源的 URL。比如，服务器上有某个 HTML 文档的英语和法语版本。返回这个代码时，会带有一个选项列表，这样用户就可以选择他希望使用的那一项。有多个版本可用时，客户端需要沟通解决。

### 3.2 301 Moved Permanently（永久重定向）

状态码 `301` 表示，请求的资源已被永久移动到新的 URL，客户端应将任何未来对该资源的请求自动转到新的 URL。搜索引擎会更新索引以使用新的 URL。

```http
HTTP/1.1 301 Moved Permanently
Location: https://www.newdomain.com/newpage
```

### 3.3 302 Found（临时重定向）

状态码 `302` 表示，请求的资源临时移动到另一个 URL，客户端应使用新的 URL 进行访问，但搜索引擎不应更新索引。该状态码是 HTTP/1.0 规范的一部分。

```http
HTTP/1.1 302 Found
Location: https://www.newdomain.com/temporarypage
```

### 3.4 303 See Other（查看其他位置）

状态码 `303` 表示，客户端应使用 GET 方法请求另一个 URL，通常用于表单提交后重定向到一个结果页面。它是 HTTP/1.1 规范的一部分。

```http
HTTP/1.1 303 See Other
Location: https://www.newdomain.com/resultpage
```

### 3.5 307 Temporary Redirect（临时重定向）

状态码 `307` 表示请求的资源临时移动到另一个 URL，客户端应使用原始的 HTTP 方法（如 POST）进行重定向请求。与 `302` 类似，但更严格，明确要求使用原始方法。

```http
HTTP/1.1 307 Temporary Redirect
Location: https://www.newdomain.com/temporarypage
```

### 3.6 308 Permanent Redirect（永久重定向）

状态码 `308` 表示请求的资源已被永久移动到新的 URL，客户端应将任何未来对该资源的请求自动转到新的 URL，并使用原始 HTTP 方法。与 `301` 类似，但更严格，明确要求使用原始方法。

```http
HTTP/1.1 308 Permanent Redirect
Location: https://www.newdomain.com/newpage
```

## 四、实现 HTTP 重定向

HTTP 重定向通过在响应头中设置 `Location` 字段来实现，以下是一些常见的重定向实现方式：

### 4.1 使用服务器配置

多数 Web 服务器（如 Apache、Nginx）支持通过配置文件实现重定向。

#### （1）Nginx

在 Nginx 配置文件中，可以使用 `rewrite` 或 `return` 指令实现重定向。

```nginx
# 301 永久重定向
server {
  listen 80;
  server_name www.olddomain.com;
  return 301 https://www.newdomain.com$request_uri;
}

# 302 临时重定向
server {
  listen 80;
  server_name www.olddomain.com;
  return 302 https://www.newdomain.com$request_uri;
}
```

#### （2）Apache

在 Apache 配置文件中，可以使用 `Redirect` 或 `RewriteRule` 指令实现重定向。

```apache
# 301 永久重定向
<VirtualHost *:80>
  ServerName www.olddomain.com
  Redirect 301 / https://www.newdomain.com/
</VirtualHost>

# 302 临时重定向
<VirtualHost *:80>
  ServerName www.olddomain.com
  Redirect 302 / https://www.newdomain.com/
</VirtualHost>
```

#### （3）Node.js（Express 框架）

在后端代码中，可以通过设置响应头来实现重定向。

```javascript
// 301 永久重定向
app.get('/oldpage', (req, res) => {
  res.redirect(301, 'https://www.newdomain.com/newpage');
});

// 302 临时重定向
app.get('/temporarypage', (req, res) => {
  res.redirect(302, 'https://www.newdomain.com/temporarypage');
});
```

## 五、重定向的应用场景

### 5.1 域名变更

当网站迁移到新的域名时，可以使用 `301` 永久重定向将旧域名的流量引导到新域名。

```nginx
server {
  listen 80;
  server_name www.olddomain.com;
  return 301 https://www.newdomain.com$request_uri;
}
```

### 5.2 页面迁移

当网站中的某个页面被移动到新的 URL 时，可以使用 `301` 永久重定向将旧页面的流量引导到新页面。

```nginx
server {
  listen 80;
  server_name www.example.com;
  location /oldpage {
    return 301 /newpage;
  }
}
```

### 5.3 HTTPS 强制跳转

为了提高网站安全性，可以使用 `301` 永久重定向将所有 HTTP 请求重定向到 HTTPS。

```nginx
server {
  listen 80;
  server_name www.example.com;
  return 301 https://www.example.com$request_uri;
}
```

### 5.4 多语言网站

基于用户的语言偏好或地理位置，可以使用 `302` 临时重定向将用户引导到相应的语言版本网站。

```nginx
server {
  listen 80;
  server_name www.example.com;
  location / {
    if ($http_accept_language ~* "zh") {
      return 302 https://www.example.com/zh;
    }
    if ($http_accept_language ~* "en") {
      return 302 https://www.example.com/en;
    }
  }
}
```

## 六、避免重定向循环

重定向循环（Redirect Loop）是指多个重定向形成一个环，导致客户端陷入无限重定向的情况。为了避免重定向循环，需确保重定向逻辑正确，不会导致重复或相互重定向。

以下代码，是一个错误的重定向导致循环的例子。

```nginx
server {
  listen 80;
  server_name www.example.com;
  
  location /oldpage {
    return 301 /newpage;
  }

  location /newpage {
    return 301 /oldpage;
  }
}
```

正确的重定向避免循环：

```nginx
server {
  listen 80;
  server_name www.example.com;
  
  location /oldpage {
    return 301 /newpage;
  }
}
```

## 七、重定向的影响

### 7.1 SEO 影响

- **301 重定向**：搜索引擎会将旧 URL 的权重传递到新 URL，有助于保持网站排名和索引。
- **302 重定向**：搜索引擎不会更新索引，旧 URL 仍然保留在索引中，适用于临时重定向。

### 7.2 用户体验

合理使用重定向可以提升用户体验，例如将用户引导到正确的页面或语言版本，避免出现 404 错误页面。

### 7.3 性能影响

重定向会增加一次 HTTP 请求和响应的开销，可能会稍微增加页面加载时间。因此，应尽量减少不必要的重定向。

通过合理使用和配置 HTTP 重定向，可以有效地管理网站资源，提升用户体验和 SEO 排名。在实际应用中，开发者应根据具体需求选择合适的重定向类型，并注意避免常见问题如重定向循环等。
