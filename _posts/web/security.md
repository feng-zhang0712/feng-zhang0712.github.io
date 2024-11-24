# Web 安全

## 一、HTTP 安全

### 1.1 内容安全策略（Content Security Policy, CSP）

#### （1）介绍

内容安全策略是通过 HTTP 头或 `<meta>` 标签来定义的安全策略，用于指定哪些资源可以被浏览器加载和执行。CSP 的主要目标是防止各种内容注入攻击，如跨站脚本攻击（Cross-Site Scripting, XSS）、数据注入攻击等。

CSP 通过定义一组策略指令来控制资源的加载和执行。每个指令指定了允许加载和执行的资源类型及其来源。浏览器在加载页面时，会根据 CSP 指令来决定是否允许加载和执行指定的资源。

CSP 头部使用 `Content-Security-Policy` 字段来定义策略，可以在 HTTP 响应头中设置。

```http
Content-Security-Policy: <policy-directives>
```

CSP 也可以使用 `<meta>` 标签在 HTML 文档中定义。

```html
<meta http-equiv="Content-Security-Policy" content="<policy-directives>">
```

#### （2）常见的 CSP 指令

CSP 提供了一系列指令来控制不同类型资源的加载和执行。以下是一些常见的 CSP 指令：

- `default-src` 指定默认的资源加载来源，如果其他指令未明确指定资源的加载来源，则使用 `default-src` 的值。
- `script-src` 指定允许加载和执行脚本的来源。
- `style-src` 指定允许加载和应用样式表的来源。
- `img-src` 指定允许加载图像的来源。
- `connect-src` 指定允许进行网络连接（如 XHR、WebSocket、fetch 请求等）的来源。
- `font-src` 指定允许加载字体的来源。
- `frame-src` 指定允许加载嵌入框架（如 iframe）的来源。
- `object-src` 指定允许加载对象（如 `<object>`、`<embed>`、`<applet>`）的来源。
- `media-src` 指定允许加载媒体文件（如音频、视频）的来源。

CSP 指令的值可以是具体的 URL、通配符、关键字等。常见的值包括：

- `'self'`：表示同一来源（同域名、同协议、同端口）。
- `'none'`：表示不允许加载任何资源。
- `'unsafe-inline'`：允许内联资源（如内联脚本、内联样式），不推荐使用。
- `'unsafe-eval'`：允许使用 `eval()` 函数，不推荐使用。
- `data:`：允许加载 `data:` 协议资源。
- `https://trusted.cdn.com`：指定具体的可信来源。

下面是一个示例。

```http
Content-Security-Policy: 
  default-src 'self'; 
  style-src 'self' 'unsafe-inline'; 
  script-src 'self' https://trusted.cdn.com
```

或者，使用 `<meta>` 标签的形式：

```html
<meta 
  http-equiv="Content-Security-Policy" 
  content="
    default-src 'self'; 
    style-src 'self' 'unsafe-inline'; 
    script-src 'self' https://trusted.cdn.com
  "
>
```

上面的代码表示：

- 默认情况下，只允许加载同源资源。
- 允许加载同源的样式表，并允许内联样式。
- 允许加载同源和 `https://trusted.cdn.com` 来源的脚本。

#### （3）报告机制

CSP 提供了报告机制，当违反策略时，浏览器会将违规信息发送到指定的 URL。

`report-uri`（已弃用）：指定报告信息的接收 URL。

```http
Content-Security-Policy: default-src 'self'; report-uri /csp-violation-report-endpoint/
```

`report-to` 是 `report-uri` 的替代方案，指定一组端点来接收报告信息。

```http
Content-Security-Policy: default-src 'self'; report-to csp-endpoint
Report-To: {"group":"csp-endpoint","max_age":10886400,"endpoints":[{"url":"/csp-violation-report-endpoint/"}]}
```

#### （4）CSP 的应用场景

- **防止 XSS 攻击**：CSP 可以有效防止 XSS 攻击，通过限制脚本的加载和执行来源，阻止恶意脚本的注入和执行。
- **防止数据注入攻击**：通过限制数据的加载来源，可以防止攻击者通过数据注入的方式加载和执行恶意资源。
- **提高网站安全性**：通过合理设置 CSP，可以细粒度地控制资源的加载和执行，提高网站的整体安全性，减少攻击面。

### 1.2 HTTP Strict-Transport-Security（HSTS）

#### （1）介绍

HTTP Strict-Transport-Security（HSTS）是一种 Web 安全策略机制，用于强制客户端（如浏览器）通过 HTTPS 与服务器进行通信，从而防止协议降级攻击和会话劫持。HSTS 主要通过 HTTP 响应头部字段 `Strict-Transport-Security` 实现，当客户端接收到该头部后，会在指定的时间内只通过 HTTPS 访问该服务器。HSTS 有效防止了中间人攻击（MITM）和其他基于协议降级的攻击。

- **防止协议降级攻击**：通过强制使用 HTTPS，HSTS 防止攻击者将安全的 HTTPS 连接降级为不安全的 HTTP。
- **防止会话劫持**：HTTPS 提供了加密通信，HSTS 通过强制使用 HTTPS 可以防止会话劫持。
- **提高安全性**：HSTS 确保用户与服务器之间的通信始终是加密和安全的，提高了整体安全性。

#### （2）HSTS 的工作原理

服务器通过在 HTTP 响应中添加 `Strict-Transport-Security` 头部来启用 HSTS。该头部包含两个主要参数：`max-age` 和 `includeSubDomains`。

```http
Strict-Transport-Security: max-age=<expire-time>; includeSubDomains
```

- `max-age`：指定客户端应将该策略缓存的时间（以秒为单位）。在此期间，客户端将强制使用 HTTPS 进行通信。
- `includeSubDomains`：可选参数，指示策略应应用于该域名及其所有子域名。

当客户端接收到包含 HSTS 头的响应时，会遵循以下行为：

1. **强制 HTTPS**：在 `max-age` 指定的时间内，客户端将强制使用 HTTPS 访问该服务器。
2. **缓存策略**：客户端会缓存 HSTS 策略，直到 `max-age` 过期。
3. **忽略不安全请求**：如果用户尝试通过 HTTP 访问服务器，浏览器会自动重定向到 HTTPS。

在 Express 框架中，可以使用 `helmet` 中间件来启用 HSTS。

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// 启用 HSTS
app.use(helmet.hsts({
  maxAge: 31536000, // 1 年
  includeSubDomains: true
}));

// 其他中间件和路由...

app.listen(443, () => {
  console.log('Server is running on https://www.example.com');
});
```

#### （3）HSTS 预加载

HSTS 预加载（HSTS Preloading）是一种增强的 HSTS 机制，通过将域名提交到浏览器维护的 HSTS 预加载列表中，确保浏览器在首次访问域名时就强制使用 HTTPS。

浏览器（如 Chrome、Firefox、Safari 等）维护一个内置的 HSTS 预加载列表，包含了默认启用 HSTS 的域名。用户访问这些域名时，浏览器会自动强制使用 HTTPS。

要将域名添加到 HSTS 预加载列表，必须满足以下条件：

1. 启用 HSTS 且 `max-age` 至少为 1 年（31536000 秒）。
2. 设置 `includeSubDomains` 参数。
3. 设置 `preload` 参数。
4. 提交域名到 HSTS 预加载列表网站（https://hstspreload.org）。

以下是一个符合 HSTS 预加载条件的配置示例：

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

### 1.3 X-Content-Type-Options

#### （1）介绍

`X-Content-Type-Options` 是一个 HTTP 响应头，用于防止浏览器对响应资源进行 MIME 类型的自动嗅探，从而减少 MIME 类型混淆攻击的风险。它只有一个有效的值，即 `nosniff`。

- **防止 MIME 类型混淆攻击**：通过禁止 MIME 类型嗅探，可以防止浏览器将恶意脚本文件误当作其他类型文件执行。
- **提高安全性**：确保浏览器严格按照服务器指定的 MIME 类型处理资源，减少潜在的安全风险。

#### （2）`X-Content-Type-Options` 的工作原理

当服务器在响应头中设置 `X-Content-Type-Options: nosniff` 时，浏览器将遵循以下行为：

- **对于样式表（CSS）文件**：如果 Content-Type 不是 `text/css`，则浏览器将拒绝加载该样式表文件。
- **对于脚本文件（JavaScript）**：如果 Content-Type 不是 `application/javascript` 或 `text/javascript`，则浏览器将拒绝执行该脚本文件。

通过这种方式，可以有效防止浏览器将恶意脚本或样式表文件误当作其他类型文件加载或执行，从而提高 Web 应用的安全性。

在 Express 框架中，可以使用 `helmet` 中间件来启用 `X-Content-Type-Options`。

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// 启用 X-Content-Type-Options
app.use(helmet.noSniff());

// 其他中间件和路由...

app.listen(80, () => {
  console.log('Server is running on http://www.example.com');
});
```

#### （3）`X-Content-Type-Options` 的应用场景

- 防止 MIME 类型混淆攻击：攻击者可能会尝试将恶意脚本文件伪装成图片或其他无害的文件类型，并通过漏洞上传到服务器。如果浏览器对这些文件进行 MIME 类型嗅探，可能会将其当作脚本文件执行，从而导致安全问题。通过设置 `X-Content-Type-Options: nosniff`，可以防止这种情况发生。
- 确保资源的正确处理：通过禁止 MIME 类型嗅探，可以确保浏览器严格按照服务器指定的 Content-Type 处理资源，避免因 MIME 类型不匹配而导致的资源加载失败或错误。

### 1.4 X-Frame-Options

`X-Frame-Options` 是一个 HTTP 响应头，用于控制浏览器是否允许当前页面在 `<iframe>`、`<frame>`、`<embed>` 等框架标签中加载。它主要用于防止点击劫持（Clickjacking）攻击。`X-Frame-Options` 有三个有效的值。

- `DENY`：表示页面不允许在任何框架中加载。
- `SAMEORIGIN`：表示页面只允许在同一域名下的框架中加载。
- `ALLOW-FROM uri`：表示页面只允许在指定的 URI 的框架中加载。

`X-Frame-Options` 通过禁止或限制页面在框架中的加载，能够有效防止攻击者利用点击劫持进行恶意操作。

#### （2）X-Frame-Options 的工作原理

当浏览器加载一个页面时，如果发现响应头中包含 `X-Frame-Options`，则会根据其值决定是否允许页面在框架中加载。

- **DENY**：浏览器完全禁止页面在任何框架中加载。
- **SAMEORIGIN**：浏览器允许页面在同一域名下的框架中加载。
- **ALLOW-FROM uri**：浏览器允许页面在指定的 URI 的框架中加载，注意这是一个较老的标准，并不被所有浏览器支持。

在 Express 框架中，可以使用 `helmet` 中间件来启用 `X-Frame-Options`。

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// 启用 X-Frame-Options
app.use(helmet.frameguard({ action: 'deny' }));
// 或者 app.use(helmet.frameguard({ action: 'sameorigin' }));
// 或者 app.use(helmet.frameguard({ action: 'allow-from', domain: 'https://trusted.com' }));

// 其他中间件和路由...

app.listen(80, () => {
  console.log('Server is running on http://www.example.com');
});
```

#### （3）X-Frame-Options 的应用场景

- 防止点击劫持：点击劫持是一种攻击，攻击者通过将目标网站嵌入到一个不可见的框架中，诱导用户点击按钮或链接，从而执行恶意操作。通过设置 `X-Frame-Options` 头，可以有效防止这种攻击。
- 限制页面加载环境：通过限制页面只能在特定的域名或来源加载，可以确保页面只在受信任的环境中显示，防止其他网站恶意嵌入和利用。

### 1.5 X-XSS-Protection

#### （1）介绍

`X-XSS-Protection` 是一个 HTTP 响应头，用于启用浏览器的跨站脚本（XSS）过滤功能，以防止 XSS 攻击。XSS 攻击是一种常见的 Web 安全漏洞，攻击者通过在网页中注入恶意脚本，窃取用户数据或执行其他恶意操作。`X-XSS-Protection` 头主要用于提升浏览器对 XSS 攻击的防护能力。

`X-XSS-Protection` 支持的值有如下几个。

- `0`：关闭浏览器的 XSS 过滤功能。
- `1`：启用浏览器的 XSS 过滤功能（通常是默认值）。
- `1; mode=block`：启用浏览器的 XSS 过滤功能，并在检测到攻击时阻止页面加载。
- `1; report=<reporting-URI>`：启用浏览器的 XSS 过滤功能，并在检测到攻击时发送报告到指定的 URI（仅部分浏览器支持）。

#### （2）X-XSS-Protection 的工作原理

当服务器在 HTTP 响应中包含 `X-XSS-Protection` 头时，浏览器会根据其值启用或禁用 XSS 过滤功能，并在检测到潜在的 XSS 攻击时采取相应的措施。

- **启用 XSS 过滤**：浏览器会扫描页面中的脚本，检测并过滤可能的 XSS 攻击代码。
- **阻止页面加载**：在检测到 XSS 攻击时，浏览器可以选择完全阻止页面的加载，以防止恶意脚本执行。
- **发送报告**：部分浏览器支持在检测到 XSS 攻击时发送报告到指定的 URI，便于开发者进行安全审计和分析。

在 Express 框架中，可以使用 `helmet` 中间件来启用 `X-XSS-Protection`。

```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// 启用 X-XSS-Protection
app.use(helmet.xssFilter({ setOnOldIE: true }));

// 其他中间件和路由...

app.listen(80, () => {
  console.log('Server is running on http://www.example.com');
});
```

#### （3）X-XSS-Protection 的应用场景

- 防止 XSS 攻击：XSS 攻击是 Web 应用中最常见的安全问题之一。通过启用 `X-XSS-Protection` 头，可以利用浏览器内置的 XSS 过滤功能，检测并拦截潜在的 XSS 攻击，从而有效提升 Web 应用的安全性。
- 提高浏览器兼容性：虽然现代 Web 应用通常采用严格的内容安全策略（CSP）来防止 XSS 攻击，但对一些较旧的浏览器来说，`X-XSS-Protection` 头仍然是一个重要的防护机制，可以提高浏览器的兼容性。

此外，虽然 `X-XSS-Protection` 是一个有效的安全机制，但现代 Web 应用通常还会结合 Content-Security-Policy (CSP) 来提供更强大的 XSS 防护。CSP 通过限制资源的加载和执行来源，可以有效防止 XSS 攻击。

## 二、跨站脚本攻击（XSS）

跨站脚本攻击（Cross-Site Scripting，简称 XSS）是一种代码注入攻击，攻击者通过在目标网站中注入恶意脚本，进而在其他用户访问该网站时执行这些恶意脚本。XSS 攻击的主要目标是窃取用户的 cookie、会话令牌或其他敏感信息，甚至是劫持用户的会话。

### 2.1 XSS 的分类

XSS 主要分为三类：存储型、反射型和基于 DOM 的 XSS。

#### （1）存储型 XSS（Stored XSS）

存储型 XSS 又称持久型 XSS，攻击者将恶意脚本提交到目标网站，存储在服务器的数据库中。当其他用户访问包含恶意脚本的页面时，这些脚本会在用户的浏览器中执行。

假设一个评论系统没有对用户提交的评论内容进行有效过滤，攻击者可以提交包含恶意脚本的评论。

```html
<script>alert('XSS');</script>
```

当其他用户查看这条评论时，脚本会在他们的浏览器中执行。

#### （2）反射型 XSS（Reflected XSS）

反射型 XSS 又称非持久型 XSS，恶意脚本通过 URL 参数注入，并在服务器处理请求后立即返回给用户。攻击者通常会诱使用户点击包含恶意脚本的链接，脚本会在用户浏览器中执行。

假设一个搜索功能将用户输入的查询参数直接返回到页面而不进行过滤，攻击者可以构造一个恶意链接。

```text
http://example.com/search?q=<script>alert('XSS');</script>
```

当用户点击这个链接时，恶意脚本会在用户浏览器中执行。

#### （3）基于 DOM 的 XSS（DOM-based XSS）

基于 DOM 的 XSS 是一种纯客户端的攻击，恶意脚本通过修改页面的 DOM 结构在客户端执行。这个类型的 XSS 不依赖于服务器的响应，而是通过 JavaScript 直接操作 DOM 来注入和执行恶意脚本。

假设一个页面的 JavaScript 代码直接将 URL 参数写入到页面中。

```javascript
document.write(location.search);
```

攻击者可以构造一个恶意链接：

```text
http://example.com/?param=<script>alert('XSS');</script>
```

当用户访问这个链接时，恶意脚本会在用户浏览器中执行。

### 2.2 前端开发中如何预防 XSS

- 输入验证和过滤：对所有用户输入的数据进行严格的验证和过滤，移除或转义可能包含恶意脚本的字符。
- 输出编码：对用户输入的数据在输出到 HTML 页面时进行编码，防止恶意脚本被执行。可以使用 JavaScript 内置的函数进行编码，例如 `encodeURIComponent` 和 `encodeHTML`

  ```javascript
  function encodeHTML(str) {
    return str.replace(/&/g, '&amp;')
      .replace(/</g, '&lt;')
      .replace(/>/g, '&gt;')
      .replace(/"/g, '&quot;')
      .replace(/'/g, '&#39;');
  }
  ```

- 内容安全策略（CSP）：配置 Content Security Policy (CSP) 来限制加载的资源类型和来源，防止外部恶意脚本的注入和执行。

  ```html
  <meta http-equiv="Content-Security-Policy" content="default-src 'self'; script-src 'self' https://trusted.cdn.com;">
  ```

- HttpOnly 和 Secure：对于敏感的 Cookie，设置 HttpOnly 和 Secure 标记，防止 JavaScript 访问和中间人攻击。

  ```http
  Set-Cookie: session_id=your_session_id; HttpOnly; Secure
  ```

- 避免直接操作 DOM：尽量避免使用 `document.write` 和 `innerHTML` 等直接操作 DOM 的方法，使用安全的 DOM 操作方法，如 `textContent` 和 `setAttribute`。

  ```javascript
  // 不安全的方法
  document.write('<div>' + userInput + '</div>');

  // 安全的方法
  const div = document.createElement('div');
  div.textContent = userInput;
  document.body.appendChild(div);
  ```

## 三、跨站请求伪造（CSRF）

跨站请求伪造（CSRF，Cross-Site Request Forgery）指攻击者冒充受信任的用户向网站发送未经授权的请求。CSRF 攻击通常利用已认证用户的身份，执行恶意操作如转账、修改账户设置、发送消息等。

### 2.1 CSRF 攻击原理

CSRF 攻击的基本原理是利用用户的身份认证状态（如 Cookie）来发送伪造的 HTTP 请求。具体步骤如下：

1. **用户登录目标网站**：用户在目标网站（如银行网站）上登录，获得身份认证状态（如会话 Cookie）。
2. **用户访问恶意网站**：用户在浏览器中打开另一个包含恶意代码的网站。
3. **恶意网站发送伪造请求**：恶意网站通过浏览器发送伪造的 HTTP 请求到目标网站。由于用户已登录，浏览器会自动附带用户的会话 Cookie，从而使请求通过身份验证。
4. **目标网站执行伪造请求**：目标网站接收到伪造请求后，认为这是用户的合法请求，并执行相应的操作。

假设有一个银行网站，其转账功能的请求如下：

```http
POST /transfer
Host: bank.example.com
Content-Type: application/x-www-form-urlencoded
Cookie: session=abcd1234
amount=1000&to_account=attacker_account
```

攻击者可以构造一个恶意网页，将上述请求嵌入其中：

```html
<!DOCTYPE html>
<html>
<body>
  <form action="http://bank.example.com/transfer" method="POST">
    <input type="hidden" name="amount" value="1000">
    <input type="hidden" name="to_account" value="attacker_account">
    <input type="submit" value="Click me!">
  </form>
  <script>
    document.forms[0].submit();
  </script>
</body>
</html>
```

当用户在已登录银行网站的情况下访问这个恶意网页时，表单会自动提交，导致银行网站执行伪造的转账请求。

### 2.2 预防 CSRF 的措施

#### （1）使用 CSRF Token

在表单请求中使用 CSRF Token，是防范 CSRF 攻击最有效的方法之一。CSRF Token 是一个唯一且不可预测的值，由服务器生成并嵌入到页面中，随后在请求中发送回服务器进行验证。实现步骤如下。

- 服务器生成 Token：服务器生成一个唯一的 Token，将其嵌入到 HTML 表单和用户会话中。
- 表单提交 Token：表单提交时，Token 作为隐藏字段或请求头一同提交。
- 服务器验证 Token：服务器验证请求中的 Token 是否与会话中的 Token 匹配。

#### （2）使用 SameSite Cookie 属性

SameSite Cookie 属性可以限制第三方网站发送请求时携带 Cookie。SameSite 有三个值：

- Strict：完全禁止第三方请求携带 Cookie。
- Lax：允许部分第三方请求携带 Cookie（如 GET 请求）。
- None：允许所有第三方请求携带 Cookie。

#### （3）验证 Referer 和 Origin 头

服务器端可以检查请求的 Referer 和 Origin 头，确保请求来自合法的来源。如果 Referer 或 Origin 头不匹配，可以拒绝请求。

## 四、点击劫持

点击劫持（Clickjacking）是一种常见的网络攻击形式，攻击者通过在透明或隐藏的框架中嵌入一个恶意网页，并诱使用户点击网页上的某个按钮或链接，从而执行非预期的操作。攻击者利用用户的点击，达到窃取信息、执行恶意操作等目的。

### 4.1 点击劫持的工作原理

1. **嵌入目标网站**：攻击者创建一个恶意网站，并在其中嵌入目标网站的页面，通常使用 `<iframe>` 标签。
2. **隐藏或透明框架**：攻击者将嵌入的目标网站页面设置为透明或隐藏，使其不可见或难以察觉。
3. **诱导用户点击**：攻击者在恶意网站上放置诱导用户点击的元素，如按钮、链接或图片。这些元素实际上覆盖在目标网站的按钮或链接上。
4. **执行操作**：用户点击恶意网站上的元素时，实际上点击的是嵌入的目标网站上的按钮或链接，从而执行未授权的操作。

```html
<!DOCTYPE html>
<html>
<body>
  <div class="cover">Click here to win a prize!</div>
  <iframe src="https://bank.example.com/transfer"></iframe>
</body>
</html>
```

上面的代码，假设攻击者想要诱使用户点击目标网站上的一个按钮，执行转账操作。用户点击 `"Click here to win a prize!"` 文本时，实际上点击的是银行网站上的转账按钮。

### 4.2 预防措施

#### （1）X-Frame-Options HTTP 头

设置 `X-Frame-Options` HTTP 头，指示浏览器是否允许一个页面被 `<iframe>` 标签嵌入。常见的值包括：

- `DENY`：完全禁止嵌入。
- `SAMEORIGIN`：允许同源的页面嵌入。
- `ALLOW-FROM uri`：允许指定的 `uri` 页面嵌入。

#### （2）Content Security Policy (CSP)

使用 Content Security Policy (CSP) 的 `frame-ancestors` 指令，控制哪些来源可以嵌入页面。

#### （3）JavaScript 防御

使用 JavaScript 检测页面是否被嵌入到 `<iframe>` 中，如果是则跳转或显示警告。

```javascript
if (self !== top) {
    top.location = self.location;
}
```

或者：

```javascript
if (window.top !== window.self) {
    document.body.innerHTML = 'This page is not allowed to be displayed in a frame.';
}
```

#### （4）用户界面（UI）调整

通过调整用户界面的设计，增加用户对页面元素的可见性和交互性，降低点击劫持的风险。例如：

- 在关键按钮或链接周围添加边框或背景，增加可见性。
- 使用双重确认对话框，确保用户执行关键操作前确认意图。

#### （5）监控和日志记录

在服务器端监控和记录用户行为，检测异常访问和点击行为，以便及时发现和响应点击劫持攻击。

## 五、网络劫持（Network Hijacking）

网络劫持（Network Hijacking）是在前端开发中指用户的网络请求被其他恶意实体截获、篡改或劫持的行为。网络劫持可能导致用户访问恶意网站、数据泄露、请求被篡改等安全问题。网络劫持通常发生在用户和服务器之间的通信过程中，常见的攻击方式包括中间人攻击（MITM）、DNS 劫持、HTTP 劫持等。

### 5.1 常见的网络劫持方式

- 中间人攻击（Man-in-the-Middle, MITM）：攻击者通过截获用户和服务器之间的通信，能够监听、篡改或伪造通信内容。MITM 攻击可以发生在公共 Wi-Fi、被劫持的路由器或被感染的设备上。
- DNS 劫持：攻击者通过篡改 DNS 请求或响应，将用户引导至恶意网站。这可以通过感染 DNS 服务器、篡改本地 DNS 配置或利用恶意软件实现。
- HTTP 劫持：攻击者通过篡改 HTTP 请求或响应内容，插入广告、恶意代码或重定向用户到恶意网站。HTTP 劫持通常发生在未加密的 HTTP 通信中。

### 5.2 预防网络劫持的措施

- 使用 HTTPS 加密通信：使用 HTTPS 加密用户和服务器之间的通信，确保数据在传输过程中不被篡改或截获。HTTPS 通过 SSL/TLS 协议提供加密和身份验证，防止中间人攻击。
- 使用 DNSSEC：DNSSEC（DNS Security Extensions）通过数字签名验证 DNS 数据的完整性和真实性，防止 DNS 劫持。
- 使用 Content Security Policy (CSP)：CSP（内容安全策略）是一种 HTTP 响应头，帮助防止跨站脚本（XSS）和数据注入攻击。CSP 限制和控制资源加载的来源，防止恶意内容加载和执行。
- 输入和输出验证：对用户输入的数据进行严格的验证和过滤，防止恶意数据注入。对输出到页面的数据进行编码，防止跨站脚本攻击。
- 使用安全的网络环境：避免使用公共 Wi-Fi 网络，特别是在处理敏感信息或进行登录、支付等操作时。使用虚拟专用网（VPN）加密网络流量，确保数据在传输过程中不被截获。
- HTTP 安全头：配置 HTTP 安全头，增强应用的安全性，防止多种常见的攻击方式。常用的 HTTP 安全头有如下几种：
  - **Strict-Transport-Security**：强制使用 HTTPS。
  - **Content-Security-Policy**：控制资源加载来源。
  - **X-Content-Type-Options**：防止 MIME 类型嗅探。
  - **X-Frame-Options**：防止点击劫持。
  - **X-XSS-Protection**：启用浏览器的 XSS 过滤器。

## 六、SQL 注入（SQL Injection）

SQL 注入（SQL Injection）是攻击者通过将恶意 SQL 代码注入到应用程序的输入字段，从而操纵数据库执行未授权操作的攻击方式。SQL 注入攻击可能导致数据泄露、数据篡改、删除数据、执行管理操作等，严重威胁数据库的安全性。

### 6.1 SQL 注入的基本原理

SQL 注入攻击的基本原理是利用应用程序中对用户输入处理的漏洞，将恶意 SQL 代码注入到 SQL 查询中，使数据库执行攻击者构造的恶意操作。常见的注入点包括登录表单、搜索框、URL 参数等。

假设有一个登录表单，用户输入用户名和密码，应用程序通过以下 SQL 查询验证用户身份：

```sql
SELECT * FROM users WHERE username = 'user' AND password = 'pass';
```

如果应用程序直接将用户输入拼接到 SQL 查询中，没有进行任何过滤或转义：

```python
username = request.form['username']
password = request.form['password']
query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}';"
```

攻击者可以输入以下内容进行 SQL 注入：

- 用户名：`' OR '1'='1`
- 密码：`' OR '1'='1`

结果查询变成：

```sql
SELECT * FROM users WHERE username = '' OR '1'='1' AND password = '' OR '1'='1';
```

这条查询总是返回所有用户，从而绕过身份验证。

### 6.2 SQL 注入的类型

#### （1）基本 SQL 注入

攻击者直接将恶意 SQL 代码注入到查询中，使数据库执行非预期操作。

#### （2）盲注（Blind SQL Injection）

攻击者无法直接看到查询结果，但可以通过观察应用程序的响应或行为推断注入的效果。盲注通常分为两种：

- **基于布尔的盲注**：通过构造布尔条件（如 `AND '1'='1'` 或 `AND '1'='0'`）观察响应的不同。
- **基于时间的盲注**：通过构造延迟查询（如 `AND IF(condition, SLEEP(5), 0)`）观察响应时间的不同。

#### （3）联合查询注入（Union-based SQL Injection）

攻击者使用 `UNION` 关键字，将恶意查询与原始查询结果合并，获取数据库中的敏感信息。

#### （4）堆叠查询注入（Stacked Queries）

攻击者通过在查询中使用分号（`;`）分隔多个查询，执行多个独立的 SQL 语句。例如：`' OR '1'='1'; DROP TABLE users;-- `。

### 6.3 预防措施

防护 SQL 注入需要从多方面入手，以下是一些常用的防御方法：

#### （1）使用参数化查询（预处理语句）

参数化查询（或预处理语句）能够将 SQL 代码与数据分开处理，防止拼接过程中注入恶意代码。

示例：Python 的 `sqlite3` 模块：

```python
import sqlite3

connection = sqlite3.connect('database.db')
cursor = connection.cursor()

username = request.form['username']
password = request.form['password']

query = "SELECT * FROM users WHERE username = ? AND password = ?;"
cursor.execute(query, (username, password))
results = cursor.fetchall()
```

#### （2）使用 ORM（对象关系映射）

使用 ORM 框架（如 SQLAlchemy、Django ORM 等）来自动生成和执行 SQL 查询，避免手动拼接查询语句。

示例：使用 SQLAlchemy：

```python
from sqlalchemy.orm import sessionmaker
from sqlalchemy import create_engine
from my_models import User

engine = create_engine('sqlite:///database.db')
Session = sessionmaker(bind=engine)
session = Session()

username = request.form['username']
password = request.form['password']

user = session.query(User).filter_by(username=username, password=password).first()
```

#### （3）输入验证和过滤

严格验证和过滤所有用户输入，确保输入数据符合预期格式，移除或转义特殊字符。

示例：Python 的 `re` 模块：

```python
import re

def validate_input(input_string):
  if not re.match("^[a-zA-Z0-9_]*$", input_string):
    raise ValueError("Invalid input")
  return input_string

username = validate_input(request.form['username'])
password = validate_input(request.form['password'])
```

#### （4）最小权限原则

为数据库用户分配最低必要权限，限制其能够执行的操作和访问的数据库对象。

示例：MySQL：

```sql
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'password';
GRANT SELECT, INSERT, UPDATE, DELETE ON my_database.* TO 'app_user'@'localhost';
```

#### （5）使用存储过程

使用存储过程替代直接执行 SQL 查询，并在存储过程中进行参数验证和处理。

示例：MySQL 存储过程：

```sql
DELIMITER //

CREATE PROCEDURE authenticate_user(
  IN username VARCHAR(255),
  IN password VARCHAR(255)
)
BEGIN
  SELECT * FROM users WHERE username = username AND password = password;
END //

DELIMITER ;
```

#### （6）错误信息隐藏

隐藏详细的数据库错误信息，防止攻击者获取数据库结构和敏感信息。

示例：Python 的 Flask 框架：

```python
@app.errorhandler(500)
def handle_internal_error(error):
  return "Internal Server Error", 500
```





DOS 和 DDOS 的原理及防御方式
1. DOS（Denial of Service）攻击
原理
DOS 攻击（拒绝服务攻击）是指攻击者通过向目标服务器发送大量请求，消耗其资源（如带宽、CPU、内存等），使目标服务器无法正常响应合法用户的请求。DOS 攻击的目的在于使服务瘫痪或不可用。
常见的 DOS 攻击类型包括：
ICMP Flood：
攻击者发送大量的 ICMP Echo 请求（ping）消息，消耗目标主机的带宽和处理能力。
SYN Flood：
攻击者发送大量的 TCP SYN 请求（通常是伪造的源地址），使目标服务器忙于处理这些伪造的连接请求，占用其资源。
UDP Flood：
攻击者向目标主机发送大量的 UDP 数据包，试图使目标主机处理这些数据包，消耗其资源。
HTTP Flood：
攻击者发送大量的 HTTP 请求，使目标服务器的 Web 服务资源耗尽，无法响应正常用户的请求。
防御方式
网络硬件防火墙和过滤器：
使用硬件防火墙和过滤器来检测和阻止异常流量。
限速和流量控制：
实施限速策略，限制单个 IP 地址的连接速率和请求数量。
IP 黑白名单：
通过维护 IP 黑白名单，阻止已知的恶意 IP 地址。
使用抗 DOS 工具：
使用专业的抗 DOS 工具和服务（如 Cloudflare、Akamai 等），这些工具可以自动检测和缓解 DOS 攻击。
系统资源优化：
优化系统配置，提高服务器的处理能力和资源利用率。
2. DDOS（Distributed Denial of Service）攻击
原理
DDOS 攻击（分布式拒绝服务攻击）是指攻击者通过控制大量的僵尸网络（Botnet），从多个分布在不同地理位置的设备向目标服务器发送大量请求，导致目标服务器无法正常工作。与 DOS 攻击不同，DDOS 攻击具有更高的破坏力和复杂性，因为它是从多个源同时发起的。
常见的 DDOS 攻击类型包括：
Bandwidth Saturation：
攻击者通过发送大量流量占用目标网络的带宽，使合法用户无法访问。
Application Layer Attacks：
攻击者针对特定的应用层服务（如 HTTP、DNS、SMTP 等）发送大量请求，使应用服务耗尽资源。
Protocol Attacks：
攻击者利用协议漏洞或实现缺陷，发送畸形或恶意数据包，使目标服务崩溃或消耗资源。
防御方式
分布式防护和内容分发网络（CDN）：
使用 CDN 和分布式防护方案，将流量分散到多个分布式节点，减轻单个服务器的压力。
流量分析和异常检测：
实施流量分析工具，实时监控网络流量，检测和阻止异常流量。
自动化防护系统：
部署自动化防护系统，能够在攻击发生时自动调整防护策略和扩展资源。
冗余和负载均衡：
增加服务器的冗余和使用负载均衡，将流量分散到多个服务器，避免单点故障。
使用专业的 DDOS 防护服务：
使用专业的 DDOS 防护服务（如 Cloudflare、Akamai、Amazon Shield 等），这些服务提供强大的防护能力和专业的缓解方案。
IP 黑白名单和地理封锁：
通过维护 IP 黑白名单和实施地理封锁，阻止已知的恶意 IP 地址和特定地区的流量。
防火墙和安全网关：
配置防火墙和安全网关，设置严格的访问控制策略和流量过滤规则。
应用层防护：
在应用层实施防护措施，如 WAF（Web 应用防火墙）和限速策略，防止应用层攻击。