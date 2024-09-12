# 单点登录

## 一、背景

## 二、Cookie 和 Session 的问题

## 三、什么是单点登录？

单点登录（Single Sign-On，简称 SSO）是一种身份验证方式，允许用户使用一个账户登录多个独立的软件系统。SSO 的目标是简化用户管理和提高用户体验，同时确保系统的安全性。

通过单点登录，用户只需进行一次身份验证，就可以访问多个相关但独立的系统或应用程序。SSO 通过集中管理用户身份，并在各个系统之间共享认证状态，实现无缝登录体验。

## 四、单点登录的工作原理

SSO 的工作原理通常涉及以下步骤：

1. **用户请求访问应用 A**：用户尝试访问受保护的资源。
2. **应用 A 检查用户身份**：应用 A 检查用户是否已登录，若未登录，将用户重定向到 SSO 服务器。
3. **用户在 SSO 服务器上登录**：用户在 SSO 服务器上进行身份验证（如输入用户名和密码）。
4. **SSO 服务器生成令牌**：验证成功后，SSO 服务器生成一个令牌（Token），并将其发送回应用 A。
5. **应用 A 验证令牌**：应用 A 验证令牌的有效性，并创建本地会话。
6. **用户访问其他应用 B**：用户尝试访问应用 B，应用 B 重定向到 SSO 服务器验证令牌。
7. **SSO 服务器确认身份**：SSO 服务器确认令牌有效，应用 B 创建本地会话。

## 五、单点登录的实现方式

### 5.1 SAML 单点登录

#### （1）概述

SAML 是一种基于 XML 的标准，用于在不同安全域之间交换认证和授权数据。SAML 允许用户在不同的应用系统之间无缝地切换，并且只需要登录一次。

SAML 由以下主要部分组成：

- **SAML 断言（Assertion）**：包含用户身份和权限信息的安全声明。
- **SAML 协议（Protocol）**：定义如何请求和接收 SAML 断言。
- **SAML 绑定（Binding）**：定义如何将 SAML 协议映射到传输协议（如 HTTP）。
- **SAML 配置文件（Profile）**：定义具体的使用场景和流程。

#### （2）SAML 单点登录流程

SAML SSO 的流程涉及三个主要角色：

- **用户（User）**：需要访问受保护资源的主体。
- **身份提供者（Identity Provider, IdP）**：负责验证用户身份并生成 SAML 断言。
- **服务提供者（Service Provider, SP）**：提供受保护资源，并根据 SAML 断言授予用户访问权限。

SAML SSO 的典型流程如下。

1. **用户访问服务提供者**：用户通过浏览器访问服务提供者的受保护资源。
2. **服务提供者重定向到身份提供者**：服务提供者检测到用户未登录，将用户重定向到身份提供者进行身份验证。
3. **用户在身份提供者登录**：用户在身份提供者提供的登录页面输入凭证（如用户名和密码）进行身份验证。
4. **身份验证成功生成 SAML 断言**：身份提供者验证用户身份成功后，生成包含用户身份信息的 SAML 断言。
5. **SAML 断言传递给服务提供者**：身份提供者通过用户浏览器将 SAML 断言传递给服务提供者。
6. **服务提供者验证 SAML 断言**：服务提供者验证接收到的 SAML 断言的真实性和完整性。
7. **用户访问受保护资源**：验证通过后，服务提供者授予用户访问受保护资源的权限。

#### （3）SAML 断言类型

SAML 断言是 SAML 协议中的核心元素，包含用户身份和权限的声明。SAML 断言主要有以下三种类型：

- **认证断言（Authentication Assertion）**：声明用户已经通过身份验证。
- **属性断言（Attribute Assertion）**：声明用户的属性信息（如用户名、电子邮件等）。
- **授权决策断言（Authorization Decision Assertion）**：声明用户是否被授权访问特定资源。

#### （4）SAML 绑定和配置文件

SAML 绑定定义了如何将 SAML 协议映射到具体的传输协议。常见的绑定方式包括：

- **HTTP 重定向绑定**：使用 HTTP GET 方法通过 URL 重定向传递 SAML 消息。
- **HTTP POST 绑定**：使用 HTTP POST 方法通过表单数据传递 SAML 消息。
- **HTTP Artifact 绑定**：通过 URL 传递引用（Artifact），实际消息通过后台通道传输。

SAML 配置文件定义了特定场景下的使用流程和规范。常见的 SAML 配置文件包括：

- **Web 浏览器 SSO 配置文件**：定义了基于 Web 浏览器的 SSO 流程，是最常用的配置文件。
- **身份提供者发起的 SSO**：身份提供者直接发起 SSO 过程。
- **服务提供者发起的 SSO**：服务提供者重定向用户到身份提供者进行身份验证。

以下是一个简化的 SAML SSO 示例，展示了服务提供者和身份提供者之间的交互。

#### 服务提供者（SP）代码（伪代码）

```javascript
const express = require('express');
const saml2 = require('saml2-js');
const app = express();

// 创建服务提供者对象
const sp = new saml2.ServiceProvider({
  entity_id: "https://sp.example.com/metadata",
  private_key: fs.readFileSync("path/to/sp-private-key.pem").toString(),
  certificate: fs.readFileSync("path/to/sp-certificate.pem").toString(),
  assert_endpoint: "https://sp.example.com/assert"
});

// 创建身份提供者对象
const idp = new saml2.IdentityProvider({
  sso_login_url: "https://idp.example.com/login",
  certificates: [fs.readFileSync("path/to/idp-certificate.pem").toString()]
});

// 服务提供者发起 SSO 请求
app.get("/login", (req, res) => {
  sp.create_login_request_url(idp, {}, (err, login_url) => {
    if (err) return res.send(500);
    res.redirect(login_url);
  });
});

// 服务提供者处理 SSO 响应
app.post("/assert", (req, res) => {
  sp.post_assert(idp, { request_body: req.body }, (err, saml_response) => {
    if (err) return res.send(500);
    // 验证成功，处理用户登录逻辑
    const user = saml_response.user;
    req.session.user = user;
    res.redirect("/protected");
  });
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

#### 身份提供者（IdP）代码（伪代码）

```javascript
const express = require('express');
const saml2 = require('saml2-js');
const app = express();

// 创建身份提供者对象
const idp = new saml2.IdentityProvider({
  sso_login_url: "https://idp.example.com/login",
  certificates: [fs.readFileSync("path/to/idp-certificate.pem").toString()]
});

// 身份提供者处理登录请求
app.post("/login", (req, res) => {
  const user = authenticate(req.body.username, req.body.password); // 伪代码
  if (!user) return res.status(401).send("Invalid credentials");

  const sp = new saml2.ServiceProvider({
    entity_id: "https://sp.example.com/metadata",
    private_key: fs.readFileSync("path/to/sp-private-key.pem").toString(),
    certificate: fs.readFileSync("path/to/sp-certificate.pem").toString(),
    assert_endpoint: "https://sp.example.com/assert"
  });

  idp.create_login_response(sp, {
    in_response_to: req.body.SAMLRequest,
    acs_url: req.body.ACSUrl,
    user: {
      name_id: user.id.toString(),
      attributes: {
        email: user.email,
        name: user.name
      }
    }
  }, (err, loginResponse) => {
    if (err) return res.send(500);
    res.redirect(loginResponse);
  });
});

app.listen(4000, () => {
  console.log("IdP server is running on port 4000");
});
```

### 5.2 OAuth 单点登录

#### （1）OAuth 概述

OAuth 是一种授权框架，允许第三方应用程序在用户授权的情况下访问用户的资源，而不需要用户的密码。OAuth 的主要目标是提供一种安全、便捷的授权机制，确保用户数据的安全和隐私。

OAuth 主要涉及以下四个角色：

- **资源拥有者（Resource Owner）**：即用户，拥有受保护资源的主体。
- **客户端（Client）**：第三方应用程序，代表用户访问资源。
- **授权服务器（Authorization Server）**：验证用户身份并颁发访问令牌（Access Token）。
- **资源服务器（Resource Server）**：存储用户资源，并接受访问令牌以允许访问资源。

#### （2）OAuth 授权流程

OAuth 2.0 定义了多种授权流程（授权码模式、隐式模式、密码模式、客户端凭证模式），其中最常用的是授权码模式。以下是授权码模式的典型流程：

1. **用户请求授权**：
   用户通过浏览器访问客户端（第三方应用），客户端将用户重定向到授权服务器的授权页面。

   ```html
   <a href="https://authorization-server.com/auth?response_type=code&client_id=CLIENT_ID&redirect_uri=REDIRECT_URI&scope=read">Login with Authorization Server</a>
   ```

2. **用户授权**：
   用户在授权服务器的授权页面登录并授权客户端访问其资源。

3. **授权服务器返回授权码**：
   授权服务器验证用户身份并同意授权后，将用户重定向回客户端，并附上授权码。

   ```http
   https://client-app.com/callback?code=AUTHORIZATION_CODE
   ```

4. **客户端请求访问令牌**：
   客户端通过服务器端代码向授权服务器发送 POST 请求，使用授权码交换访问令牌。

   ```http
   POST /token HTTP/1.1
   Host: authorization-server.com
   Authorization: Basic BASE64(CLIENT_ID:CLIENT_SECRET)
   Content-Type: application/x-www-form-urlencoded

   grant_type=authorization_code&code=AUTHORIZATION_CODE&redirect_uri=REDIRECT_URI
   ```

5. **授权服务器返回访问令牌**：
   授权服务器验证授权码后，返回访问令牌和（可选的）刷新令牌。

   ```json
   {
     "access_token": "ACCESS_TOKEN",
     "token_type": "Bearer",
     "expires_in": 3600,
     "refresh_token": "REFRESH_TOKEN"
   }
   ```

6. **客户端使用访问令牌请求资源**：
   客户端使用访问令牌向资源服务器请求用户资源。

   ```http
   GET /resource HTTP/1.1
   Host: resource-server.com
   Authorization: Bearer ACCESS_TOKEN
   ```

7. **资源服务器返回资源**：
   资源服务器验证访问令牌后，返回用户资源。

#### （3）OAuth 授权类型

- **授权码模式**：授权码模式是最常用的授权方式，适用于服务器端应用。它通过授权码和访问令牌的两步流程，确保了授权的安全性。
- **隐式模式**：隐式模式主要用于单页应用（SPA），直接在浏览器中获取访问令牌。由于访问令牌暴露在 URL 中，安全性相对较低。
- **密码模式**：密码模式允许客户端直接使用用户的用户名和密码获取访问令牌，适用于用户信任的应用。由于需要用户的凭证，安全性较低，不推荐使用。
- **客户端凭证模式**：客户端凭证模式用于服务与服务之间的授权，客户端使用自己的凭证（如客户端 ID 和客户端密钥）获取访问令牌。

以下是一个使用 OAuth 2.0 授权码模式实现单点登录的示例。

```javascript
// 客户端代码（Node.js 伪代码）
const express = require('express');
const request = require('request');
const app = express();

const client_id = 'YOUR_CLIENT_ID';
const client_secret = 'YOUR_CLIENT_SECRET';
const redirect_uri = 'http://localhost:3000/callback';

app.get('/login', (req, res) => {
  const auth_url = `https://authorization-server.com/auth?response_type=code&client_id=${client_id}&redirect_uri=${redirect_uri}&scope=read`;
  res.redirect(auth_url);
});

app.get('/callback', (req, res) => {
  const authorization_code = req.query.code;
  const token_url = 'https://authorization-server.com/token';

  const options = {
    url: token_url,
    method: 'POST',
    auth: {
      user: client_id,
      pass: client_secret
    },
    form: {
      grant_type: 'authorization_code',
      code: authorization_code,
      redirect_uri: redirect_uri
    }
  };

  request(options, (error, response, body) => {
    if (error) {
      res.send('Error exchanging authorization code for access token');
      return;
    }
    const access_token = JSON.parse(body).access_token;
    req.session.access_token = access_token;
    res.redirect('/profile');
  });
});

app.get('/profile', (req, res) => {
  const access_token = req.session.access_token;
  const options = {
    url: 'https://resource-server.com/resource',
    method: 'GET',
    headers: {
      'Authorization': `Bearer ${access_token}`
    }
  };

  request(options, (error, response, body) => {
    if (error) {
      res.send('Error fetching resource');
      return;
    }
    res.send(body);
  });
});

app.listen(3000, () => {
  console.log('Client app listening on port 3000');
});
```

### 5.3 OpenID Connect 单点登录

#### （1）OpenID Connect 概述

OpenID Connect 是一种简单而灵活的身份验证协议，使用 JSON Web Tokens (JWT) 传递身份信息。它在 OAuth 2.0 授权框架的基础上增加了身份验证层，允许客户端通过授权服务器获取用户的身份信息。

OpenID Connect 涉及以下主要角色：

- **终端用户（End User）**：需要访问受保护资源的主体。
- **客户端（Client）**：请求身份验证并获取用户身份信息的应用程序。
- **身份提供者（Identity Provider, IdP）**：验证用户身份并发布身份令牌（ID Token）。
- **授权服务器（Authorization Server）**：与身份提供者通常是相同的实体，负责处理授权请求和发布访问令牌。

#### （2）OpenID Connect 流程

OpenID Connect 提供多种授权流程，其中最常用的是授权码流程和隐式流程。以下是授权码流程的典型步骤：

1. **用户请求登录**：
   用户通过浏览器访问客户端应用，请求登录。

   ```html
   <a href="https://idp.example.com/auth?response_type=code&client_id=YOUR_CLIENT_ID&redirect_uri=YOUR_REDIRECT_URI&scope=openid profile email">Login with IdP</a>
   ```

2. **用户在身份提供者登录并授权**：
   用户被重定向到身份提供者的登录页面，在此登录并同意授权客户端访问其身份信息。

3. **身份提供者返回授权码**：
   身份提供者验证用户身份并同意授权后，将用户重定向回客户端，并附上授权码。

   ```http
   https://client-app.com/callback?code=AUTHORIZATION_CODE
   ```

4. **客户端请求访问令牌和身份令牌**：
   客户端通过服务器端代码向授权服务器发送 POST 请求，使用授权码交换访问令牌和身份令牌。

   ```http
   POST /token HTTP/1.1
   Host: idp.example.com
   Authorization: Basic BASE64(CLIENT_ID:CLIENT_SECRET)
   Content-Type: application/x-www-form-urlencoded

   grant_type=authorization_code&code=AUTHORIZATION_CODE&redirect_uri=YOUR_REDIRECT_URI
   ```

5. **授权服务器返回访问令牌和身份令牌**：
   授权服务器验证授权码后，返回访问令牌和身份令牌。

   ```json
   {
     "access_token": "ACCESS_TOKEN",
     "id_token": "ID_TOKEN",
     "token_type": "Bearer",
     "expires_in": 3600
   }
   ```

6. **客户端使用身份令牌获取用户信息**：
   客户端解码和验证身份令牌，获取用户的身份信息，并根据需要存储或显示这些信息。

#### （3）OpenID Connect 令牌

##### a. 身份令牌（ID Token）

身份令牌是一个 JWT，包含用户的身份信息。ID Token 包括以下重要字段：

- **iss**：身份提供者的标识符。
- **sub**：用户的唯一标识符。
- **aud**：接收令牌的客户端标识符。
- **exp**：令牌的过期时间。
- **iat**：令牌的签发时间。
- **auth_time**：用户进行身份验证的时间。
- **nonce**：用于防止重放攻击的随机字符串。

##### b. 访问令牌（Access Token）

访问令牌用于授权客户端访问受保护资源。它是一个短期令牌，可以与资源服务器一起使用，以获取用户的受保护信息。

##### c. 刷新令牌（Refresh Token）

刷新令牌是一个长期令牌，用于获取新的访问令牌，而无需再次进行用户认证。

以下是一个使用 OpenID Connect 授权码流程实现单点登录的示例。

```javascript
// 客户端代码（Node.js 伪代码）
const express = require('express');
const request = require('request');
const jwt = require('jsonwebtoken');
const app = express();

const client_id = 'YOUR_CLIENT_ID';
const client_secret = 'YOUR_CLIENT_SECRET';
const redirect_uri = 'http://localhost:3000/callback';
const discovery_url = 'https://idp.example.com/.well-known/openid-configuration';
let token_endpoint, userinfo_endpoint;

// 获取 OpenID Connect 配置
request(discovery_url, (error, response, body) => {
  if (error) throw error;
  const config = JSON.parse(body);
  token_endpoint = config.token_endpoint;
  userinfo_endpoint = config.userinfo_endpoint;
});

app.get('/login', (req, res) => {
  const auth_url = `https://idp.example.com/auth?response_type=code&client_id=${client_id}&redirect_uri=${redirect_uri}&scope=openid profile email`;
  res.redirect(auth_url);
});

app.get('/callback', (req, res) => {
  const authorization_code = req.query.code;
  const options = {
    url: token_endpoint,
    method: 'POST',
    auth: {
      user: client_id,
      pass: client_secret
    },
    form: {
      grant_type: 'authorization_code',
      code: authorization_code,
      redirect_uri: redirect_uri
    }
  };

  request(options, (error, response, body) => {
    if (error) return res.send('Error exchanging authorization code for tokens');
    const tokens = JSON.parse(body);
    const id_token = tokens.id_token;
    const decoded_id_token = jwt.decode(id_token);
    
    req.session.user = {
      id: decoded_id_token.sub,
      name: decoded_id_token.name,
      email: decoded_id_token.email
    };
    
    res.redirect('/profile');
  });
});

app.get('/profile', (req, res) => {
  if (!req.session.user) {
    return res.redirect('/login');
  }
  
  res.send(`<h1>Profile</h1><p>Name: ${req.session.user.name}</p><p>Email: ${req.session.user.email}</p>`);
});

app.listen(3000, () => {
  console.log('Client app listening on port 3000');
});
```

## 六、单点登录的安全性

### 6.1 安全性挑战

- **单点故障**：SSO 服务器的不可用或被攻击将影响所有依赖 SSO 的应用。
- **令牌泄露**：令牌在传输和存储过程中可能被截获或泄露。
- **重放攻击**：攻击者可能重放已使用的令牌，获取未授权访问。

### 6.2 安全性防护措施

- **使用 HTTPS**：确保令牌和敏感数据在传输过程中加密。
- **短时令牌**：使用短时有效的令牌，减少令牌泄露的风险。
- **令牌存储**：在客户端使用 HttpOnly 和 Secure 属性存储令牌，防止 JavaScript 访问和跨站请求伪造（CSRF）。
- **令牌签名和加密**：使用签名和加密技术保护令牌的完整性和保密性。
- **多因素认证（MFA）**：增加额外的认证因素，提高安全性。
- **日志和监控**：记录登录和令牌使用的日志，监控异常行为。
