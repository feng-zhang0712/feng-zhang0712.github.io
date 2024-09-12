# HTTP 的 Cookie

<!-- 如何解决Cookie跨域问题 -->

## 一、概述

Cookie 是服务器发送到浏览器并保存在本地的一小块数据，浏览器每次向服务器发出请求，就会自动附上这段信息。

Cookie 的作用主要有以下三个方面：

- 对话（session）管理：保存登录状态、购物车等需要记录的信息。
- 个性化信息：保存用户的偏好，比如网页的字体大小、背景色等等。
- 追踪用户：记录和分析用户行为。

Cookie 不是一种理想的客户端存储机制。它缺乏数据操作接口，而且会影响性能。客户端存储建议使用 Web storage API 和 IndexedDB。只有那些每次请求都需要让服务器知道的信息，才应该放在 Cookie 里面。

不同浏览器对 Cookie 数量和大小的限制，是不一样的。一般来说，单个域名设置的 Cookie 不应超过30个，每个 Cookie 的大小不能超过 4KB。超过限制以后，Cookie 将被忽略，不会被设置。

用户可以设置浏览器不接受 Cookie，也可以设置不向服务器发送 Cookie。window.navigator.cookieEnabled 属性返回一个布尔值，表示浏览器是否打开 Cookie 功能。

document.cookie 属性返回当前网页的 Cookie。

注意，区分 Cookie 时不考虑协议和端口。也就是说，<http://example.com> 设置的 Cookie，可以被 <https://example.com> 或 <http://example.com:8080> 读取。

简单理解，Cookie 的工作原理为如下三个步骤。

- **设置 Cookie**：服务器通过 HTTP 响应头 `Set-Cookie` 设置 Cookie。
- **存储 Cookie**：浏览器接收到 `Set-Cookie` 后会存储在本地，并根据 `Path` 和 `Domain` 等属性进行管理。
- **发送 Cookie**：浏览器在后续请求时，根据 `Path` 和 `Domain` 自动附带相应的 Cookie 到请求头 `Cookie` 中。

## 二、Cookie 与 HTTP 协议

如果服务器希望在浏览器保存 Cookie，就要在 HTTP 回应的头信息里面，放置一个 `Set-Cookie` 字段。

```http
Set-Cookie:foo=bar
```

上面代码会在浏览器保存一个名为foo的 Cookie，它的值为bar。

一个Set-Cookie字段里面，可以同时包括多个属性，没有次序的要求。

```http
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>; Secure; HttpOnly
```

注意，如果服务器想改变一个早先设置的 Cookie，必须同时满足四个条件：Cookie 的 key、domain、path 和 secure 都匹配。只要有一个属性不同，就会生成一个全新的 Cookie，而不是替换掉原来那个 Cookie。

## 三、Cookie 的属性

### 3.1 Domain、Path

- `Domain`：指定 Cookie 属于哪个域名，格式为 `Domain=example.com`。子域名也可以访问该 Cookie。
- `Path`：指定哪些路径要附带这个 Cookie，格式为 `Path=/somepath`。只有匹配路径的请求才会附带该 Cookie。

### 3.2 Expires、Max-Age

- `Expires`：指定 Cookie 的过期时间，格式为 `Expires=Wed, 21 Oct 2024 07:28:00 GMT`。
- `Max-Age`：指定 Cookie 的有效期（秒），如 `Max-Age=3600` 表示一小时后过期。

注意，如果 `Set-Cookie` 字段没有指定 `Expires` 或 `Max-Age` 属性，那么这个 Cookie 就是 Session Cookie，即它只在本次对话存在。

### 3.3 Secure、HttpOnly

- `Name=Value`：Cookie 的名称和值，是 Cookie 的基本组成部分。
- `Secure`：指定 Cookie 仅在通过 HTTPS 协议传输时才会发送，格式为 `Secure`。
- `HttpOnly`：指定 Cookie 不能通过 JavaScript 访问，提升安全性，格式为 `HttpOnly`。

### 3.4 SameSite

`SameSite`：指定 Cookie 的跨站请求策略，用来防止 CSRF 攻击和用户追踪。

它可以设置三个值：

- `Strict`：完全禁止跨站请求附带 Cookie。
- `Lax`：允许部分跨站请求（如 GET 请求）附带 Cookie。
- `None`：允许所有跨站请求附带 Cookie，但必须结合 `Secure` 属性。

设置了 `Strict` 或 `Lax` 以后，基本就杜绝了 CSRF 攻击。当然，前提是用户浏览器支持 `SameSite` 属性。

注意，网站可以选择显式关闭 `SameSite` 属性，将其设为 `None`。不过，前提是必须同时设置 `Secure` 属性（Cookie 只能通过 HTTPS 协议发送），否则无效。

## 四、document.cookie

`document.cookie` 属性用于读写当前网页的 Cookie。

- 写入的时候，Cookie 的值必须写成 `key=value` 的形式。并且，等号两边不能有空格。
- 写入 Cookie 的时候，必须对分号、逗号和空格进行转义（它们都不允许作为 Cookie 的值），这可以用 `encodeURIComponent` 方法达到。
- `document.cookie` 一次只能写入一个 Cookie，而且写入并不是覆盖，而是添加。
- 删除一个现存 Cookie 的唯一方法，是设置它的 `expires` 属性为一个过去的日期。

`document.cookie` 读写行为的差异（一次可以读出全部 Cookie，但是只能写入一个 Cookie），与 HTTP 协议的 Cookie 通信格式有关。浏览器向服务器发送 Cookie 的时候，Cookie 字段是使用一行将所有 Cookie 全部发送；服务器向浏览器设置 Cookie 的时候，`Set-Cookie` 字段是一行设置一个 Cookie。

## 五、Cookie 的安全性

### 5.1 安全问题

- **劫持**：Cookie 在传输过程中可能被攻击者截获。
- **伪造**：攻击者可能伪造 Cookie 进行未授权访问。
- **跨站脚本攻击（XSS）**：恶意脚本可能读取和利用 Cookie。

### 5.2 防护措施

- **使用 HTTPS**：结合 `Secure` 属性，确保 Cookie 在传输过程中加密。
- **设置 HttpOnly**：防止 JavaScript 读取 Cookie，减少 XSS 攻击风险。
- **设置 SameSite**：防止 CSRF 攻击，控制跨站请求附带 Cookie 的行为。
- **定期更新 Cookie**：定期更新和验证 Cookie，避免长期使用同一个 Cookie 增加风险。

参考：

- [HTTP Cookie](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)
- [Cookie](https://wangdoc.com/javascript/bom/cookie)