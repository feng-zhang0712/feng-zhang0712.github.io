# HTML5 离线存储机制

## 一、Cookie

### 1.1 什么是 Cookie

**Cookie** 是一种在计算机终端存储少量文本数据的工具，主要用于会话管理、用户识别和跟踪。尽管随着现代 Web API 的发展，如 `localStorage` 和 `sessionStorage`，Cookie 的使用场景有所减弱，但它依然在某些特定场景下不可或缺。

Cookie 由服务器生成并发送至客户端（浏览器），然后浏览器会在后续对该服务器的请求中携带该 Cookie 数据。Cookie 以键值对的形式存储，每个 Cookie 都包含一些属性，用于控制其行为。

一般来说，每个 Cookie 大小限制为 4KB，单个域名下最多可以存储 20 个 Cookie。此外，Cookie 数据可能会被劫持或伪造，特别是当没有启用 `secure` 和 `HttpOnly` 属性的时候。

### 1.2 Cookie 的属性

- **name**：Cookie 的名称。
- **value**：Cookie 的值。
- **domain**：指定 Cookie 可用的域。
- **path**：指定 Cookie 可用的路径。
- **expires** 或 **max-age**：Cookie 的有效期，`expires` 是具体的过期时间点，而 `max-age` 是从现在起的存活时间（秒）。
- **secure**：指定 Cookie 仅在 HTTPS 连接中传输。
- **HttpOnly**：指定 Cookie 只能在 HTTP 请求中使用，无法通过 JavaScript 访问。
- **SameSite**：控制 Cookie 在跨站请求时的行为，有三个值可供选择：`Strict`、`Lax` 和 `None`。

在 Cookie 中，与安全性相关的字段有三个。

- `HttpOnly` 属性可以防止 JavaScript 访问 Cookie 数据，从而降低 XSS 攻击风险。
- `Secure` 属性指定 Cookie 只能在 HTTPS 连接中传输，防止 Cookie 数据被窃听。
- `SameSite` 属性可以防止 CSRF 攻击，限制跨站请求时发送 Cookie 的行为。

### 1.3 设置 Cookie

服务器可以在响应头中通过 `Set-Cookie` 设置 Cookie。

```http
HTTP/1.1 200 OK
Set-Cookie: username=JohnDoe; Expires=Wed, 21 Oct 2024 07:28:00 GMT; Secure; HttpOnly
```

在客户端，可以使用 JavaScript 设置 Cookie。

```javascript
// 设置 Cookie
document.cookie = "username=JohnDoe; expires=Wed, 21 Oct 2024 07:28:00 GMT; path=/";

// 获取 Cookie
function getCookie(name) {
  let cookieArr = document.cookie.split(';');
  for(let i = 0; i < cookieArr.length; i++) {
    let cookiePair = cookieArr[i].split('=');
    if(name == cookiePair[0].trim()) {
      return decodeURIComponent(cookiePair[1]);
    }
  }
  return null;
}

// 删除 Cookie
document.cookie = "username=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/";
```

### 1.4 Cookie 的使用场景

#### （1）会话管理

使用 Cookie 存储会话标识符，用于服务器识别用户的会话状态。

```http
Set-Cookie: sessionId=abc123; Path=/; HttpOnly
```

服务器通过 `sessionId` 识别用户的会话，并在后续请求中保持会话状态。

#### （2）用户偏好设置

存储用户偏好设置，如语言选择、主题设置等。

```javascript
document.cookie = "language=zh-CN; expires=Fri, 31 Dec 2024 23:59:59 GMT; path=/";
```

通过读取 Cookie，网站可以自动应用用户的偏好设置。

#### （3）跟踪和分析

跟踪用户在网站上的行为，用于分析和广告投放。

```http
Set-Cookie: trackingId=abc123; Path=/; HttpOnly
```

通过 `trackingId`，分析工具可以汇总用户的行为数据。

## 二、localStorage

### 2.1 什么是 localStorage

`localStorage` 是一种基于 Web 的客户端存储机制，用于在用户浏览器中持久化存储数据。与 `sessionStorage` 不同，`localStorage` 中存储的数据在浏览器会话结束（即关闭浏览器）后仍然存在，除非手动删除。

`localStorage` 具有如下特点：

- 各浏览器对 `localStorage` 的存储容量限制不同，但一般为 5MB。如果超出容量限制，会抛出 `QuotaExceededError` 异常。
- `localStorage` 中的数据是明文存储的，任何人都可以通过浏览器开发者工具查看。因此，不要在 `localStorage` 中存储敏感信息（如密码、个人身份信息等）。
- `localStorage` 遵循同源策略，即只有相同的协议、域名和端口的页面才能访问相同的 `localStorage` 数据。

### 2.2 localStorage 常见操作

- `setItem`：用来存储数据。键和值都是字符串类型。
- `getItem`：用来获取数据。如果键不存在，会返回 `null`。
- `removeItem`：用来删除指定键的数据。
- `clear`：用来清空 `localStorage` 中的所有数据。

### 2.3 localStorage 应用场景

#### （1）用户设置

开发者可以使用 `localStorage` 来持久化存储用户的设置，如主题、语言偏好等。

```javascript
// 存储用户的主题设置
localStorage.setItem('theme', 'dark');

// 获取用户的主题设置
const theme = localStorage.getItem('theme');
document.body.className = theme;
```

#### （2）持久登录状态

可以存储用户的登录令牌，以便在用户下次访问时保持登录状态。

```javascript
// 存储用户的登录令牌
localStorage.setItem('authToken', 'your-auth-token');

// 获取用户的登录令牌
const authToken = localStorage.getItem('authToken');
```

#### （3）购物车数据

电商网站可以使用 `localStorage` 来存储购物车数据，使购物车内容在页面刷新或浏览器重启后依然保持。

```javascript
// 添加商品到购物车
function addToCart(productId) {
  let cart = JSON.parse(localStorage.getItem('cart')) || [];
  cart.push(productId);
  localStorage.setItem('cart', JSON.stringify(cart));
}

// 获取购物车数据
function getCart() {
  return JSON.parse(localStorage.getItem('cart')) || [];
}
```

### 2.4 数据存储格式

虽然 `localStorage` 只能存储字符串类型的数据，但可以通过 JSON 来存储更复杂的数据结构。

```javascript
const user = {
  name: 'John Doe',
  age: 30,
  email: 'johndoe@example.com'
};

// 将对象转换为 JSON 字符串并存储
localStorage.setItem('user', JSON.stringify(user));

// 获取数据并解析为对象
const storedUser = JSON.parse(localStorage.getItem('user'));
console.log(storedUser.name);  // 输出: John Doe
```

## 三、sessionStorage

### 3.1 什么是 sessionStorage

`sessionStorage` 是一种基于 Web 的客户端存储机制，用于在浏览器会话期间存储数据。`sessionStorage` 中存储的数据仅在同一会话（即同一浏览器窗口或标签页）中存在，一旦会话结束（即关闭浏览器窗口或标签页），数据即被删除。

`sessionStorage` 有如下特点特点：

- 各浏览器对 `sessionStorage` 的存储容量限制不同，但一般为 5MB。如果超出容量限制，会抛出 `QuotaExceededError` 异常。
- `sessionStorage` 中的数据是明文存储的，任何人都可以通过浏览器开发者工具查看。因此，不要在 `sessionStorage` 中存储敏感信息（如密码、个人身份信息等）。
-`sessionStorage` 遵循同源策略，即只有相同的协议、域名和端口的页面才能访问相同的 `sessionStorage` 数据。

### 3.2 sessionStorage 常见操作

- `setItem`：用来存储数据。键和值都是字符串类型。
- `getItem`：用来获取数据。如果键不存在，会返回 `null`。
- `removeItem`：用来删除指定键的数据。
- `clear`：用来清空 `sessionStorage` 中的所有数据。

### 3.3 sessionStorage 应用场景

#### （1）临时表单数据

在用户填写表单时，可以使用 `sessionStorage` 临时存储表单数据，以防止数据丢失。

```javascript
// 存储表单数据
document.getElementById('myForm').onsubmit = function() {
  sessionStorage.setItem('formData', JSON.stringify({ name: this.name.value, email: this.email.value }));
};

// 获取表单数据
const formData = JSON.parse(sessionStorage.getItem('formData'));
if (formData) {
  document.getElementById('name').value = formData.name;
  document.getElementById('email').value = formData.email;
}
```

#### （2）页面间传递数据

在同一会话内的不同页面间传递数据。

```javascript
// 页面 A：存储数据
sessionStorage.setItem('sharedData', 'Some data to share');

// 页面 B：获取数据
const sharedData = sessionStorage.getItem('sharedData');
console.log(sharedData);  // 输出: Some data to share
```

#### （3）动态数据存储

在页面加载期间或会话期间临时存储需要使用的动态数据。

```javascript
// 存储动态数据
sessionStorage.setItem('dynamicData', 'This is some dynamic data');

// 获取动态数据
const dynamicData = sessionStorage.getItem('dynamicData');
console.log(dynamicData);  // 输出: This is some dynamic data
```

### 3.4 数据存储格式

虽然 `sessionStorage` 只能存储字符串类型的数据，但可以通过 JSON 来存储更复杂的数据结构。

```javascript
const user = {
  name: 'Jane Doe',
  age: 28,
  email: 'janedoe@example.com'
};

// 将对象转换为 JSON 字符串并存储
sessionStorage.setItem('user', JSON.stringify(user));

// 获取数据并解析为对象
const storedUser = JSON.parse(sessionStorage.getItem('user'));
console.log(storedUser.name);  // 输出: Jane Doe
```

## 四、IndexDB

### 4.1 什么是 IndexedDB

**IndexedDB** 是一种基于浏览器的低级 API，用于存储大量结构化数据（包括文件和二进制数据）。它是一个事务型数据库系统，允许高性能的、本地持久化存储，对于需要存储复杂数据的 Web 应用程序非常适用。

IndexedDB 具有如下特点：

- IndexedDB 是 HTML5 规范的一部分，现代浏览器均支持 IndexedDB。但不同浏览器的实现可能会有一些细微差别。
- 所有 IndexedDB 操作都是异步的，需要处理回调函数或使用 `Promise`。
- IndexedDB 遵循同源策略，不同来源的页面不能访问彼此的数据。

### 4.2 IndexedDB 主要概念

- 数据库（Database）：一个独立的数据库实例，用于存储结构化数据。每个数据库有一个名称和版本号。
- 对象存储（Object Store）：类似于关系数据库中的表，用于存储特定类型的记录。每个对象存储有一个名称和主键。
- 事务（Transaction）：用于保护一组操作的完整性，使其要么全部完成，要么全部失败。IndexedDB 中的所有读写操作都在事务中执行。
- 索引（Index）：用于加速查询的辅助数据结构。类似于关系数据库中的索引，用于快速查找数据。

### 4.3 IndexedDB 常见操作

#### （1）打开数据库

使用 `indexedDB.open` 方法打开数据库。如果数据库不存在，会自动创建。

```javascript
const request = indexedDB.open("myDatabase", 1);

request.onerror = function(event) {
  console.log("Database error: " + event.target.errorCode);
};

request.onsuccess = function(event) {
  const db = event.target.result;
  console.log("Database opened successfully");
};
```

#### （2）创建对象存储

在数据库版本更新时创建对象存储。

```javascript
request.onupgradeneeded = function(event) {
  const db = event.target.result;
  const objectStore = db.createObjectStore("users", { keyPath: "id" });

  objectStore.createIndex("name", "name", { unique: false });
  objectStore.createIndex("email", "email", { unique: true });
};
```

#### （3）添加数据

使用 `add` 方法添加数据。

```javascript
const transaction = db.transaction(["users"], "readwrite");
const objectStore = transaction.objectStore("users");

const user = { id: 1, name: "John Doe", email: "johndoe@example.com" };
const request = objectStore.add(user);

request.onsuccess = function(event) {
  console.log("User added to the store", event.target.result);
};

request.onerror = function(event) {
  console.log("Error adding user", event.target.errorCode);
};
```

#### （4）获取数据

使用 `get` 方法获取数据。

```javascript
const transaction = db.transaction(["users"]);
const objectStore = transaction.objectStore("users");

const request = objectStore.get(1);

request.onsuccess = function(event) {
  if (request.result) {
    console.log("User:", request.result);
  } else {
    console.log("No user found with this ID");
  }
};

request.onerror = function(event) {
  console.log("Error retrieving user", event.target.errorCode);
};
```

#### （5）更新数据

使用 `put` 方法更新数据。

```javascript
const transaction = db.transaction(["users"], "readwrite");
const objectStore = transaction.objectStore("users");

const updatedUser = { id: 1, name: "John Doe", email: "john.doe@example.com" };
const request = objectStore.put(updatedUser);

request.onsuccess = function(event) {
  console.log("User updated successfully");
};

request.onerror = function(event) {
  console.log("Error updating user", event.target.errorCode);
};
```

#### （6）删除数据

使用 `delete` 方法删除数据。

```javascript
const transaction = db.transaction(["users"], "readwrite");
const objectStore = transaction.objectStore("users");

const request = objectStore.delete(1);

request.onsuccess = function(event) {
  console.log("User deleted successfully");
};

request.onerror = function(event) {
  console.log("Error deleting user", event.target.errorCode);
};
```

### 4.4 数据存储格式

IndexedDB 支持存储多种类型的数据，包括对象、数组、文件等。使用 JSON 可以更方便地存储和解析复杂的对象。

```javascript
const user = {
  id: 1,
  name: 'John Doe',
  age: 30,
  email: 'johndoe@example.com'
};

// 将对象存储到 IndexedDB
const transaction = db.transaction(["users"], "readwrite");
const objectStore = transaction.objectStore("users");
const request = objectStore.add(user);

request.onsuccess = function(event) {
  console.log("User added to the store", event.target.result);
};

// 获取并解析对象
const request = objectStore.get(1);

request.onsuccess = function(event) {
  const storedUser = request.result;
  console.log(storedUser.name);  // 输出: John Doe
};
```

## 五、Service Workers

### 5.1 什么是 Service Workers

Service Workers 是一种在独立线程中运行的脚本，能够拦截和处理网络请求，管理缓存，为 Web 应用提供离线访问和推送通知等功能。它们不能直接访问 DOM，但可以通过消息传递与主线程进行通信。

Service Workers 的有如下特点：

- **离线支持**：通过缓存关键资源，提供离线访问能力。
- **后台同步**：能够在网络状况恢复时完成后台数据同步。
- **推送通知**：支持向用户推送通知。
- **拦截网络请求**：可以拦截、修改和替换网络请求。

### 5.2 Service Workers 注册和生命周期

#### （1）Service Workers 需要在应用的 JavaScript 中注册。注册过程通常在主线程的 JavaScript 文件中完成。

```javascript
if ('serviceWorker' in navigator) {
  window.addEventListener('load', function() {
    navigator.serviceWorker.register('/service-worker.js').then(function(registration) {
      console.log('Service Worker registered with scope:', registration.scope);
    }).catch(function(error) {
      console.error('Service Worker registration failed:', error);
    });
  });
}
```

#### （2）Service Workers 生命周期

1. **注册（Registering）**：在主线程中注册 Service Worker。
2. **安装（Installing）**：在第一次注册或更新时，Service Worker 进入安装阶段。
3. **激活（Activating）**：安装成功后，Service Worker 进入激活阶段。
4. **激活后（Activated）**：激活后，Service Worker 开始控制相应范围内的页面。

#### （3）生命周期事件

- **install**：在 Service Worker 安装时触发，通常用于预缓存资源。
- **activate**：在 Service Worker 激活时触发，通常用于清理旧缓存。
- **fetch**：拦截网络请求，通常用于返回缓存资源或进行网络请求。

### 3. 缓存机制

#### 3.1 Cache API

Service Workers 使用 Cache API 管理缓存资源。Cache API 提供了强大的缓存管理功能，可以存储和检索任意的请求和响应对。

**安装阶段缓存资源**：

```javascript
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('my-cache').then(function(cache) {
      return cache.addAll([
        '/',
        '/index.html',
        '/styles.css',
        '/script.js',
        '/image.png'
      ]);
    })
  );
});
```

#### 3.2 缓存策略

Service Workers 可以使用多种缓存策略来决定如何处理网络请求和缓存资源：

- **Cache First**：优先使用缓存，如果缓存中没有找到资源，则进行网络请求并缓存新的资源。
- **Network First**：优先进行网络请求，如果网络请求失败则使用缓存。
- **Cache Only**：只使用缓存，不进行网络请求。
- **Network Only**：只进行网络请求，不使用缓存。
- **Stale-While-Revalidate**：优先使用缓存，同时进行网络请求来更新缓存。

**示例：Cache First 策略**：

```javascript
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request).then(function(response) {
        return caches.open('my-cache').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});
```

### 4. Service Workers 使用场景

- 离线访问：通过缓存关键资源，提供离线访问能力，使用户在没有网络连接时仍然可以使用应用。
- 性能优化：通过缓存静态资源，减少网络请求次数，提升应用的加载速度和性能。
- 后台同步：在网络连接恢复时，Service Workers 可以自动同步数据，确保数据一致性。
- 推送通知：Service Workers 可以接收并显示推送通知，即使用户没有打开应用。

以下是一个完整的示例，展示了如何使用 Service Workers 进行缓存管理和离线访问。

**主线程 JavaScript 文件**：

```javascript
if ('serviceWorker' in navigator) {
  window.addEventListener('load', function() {
    navigator.serviceWorker.register('/service-worker.js').then(function(registration) {
      console.log('Service Worker registered with scope:', registration.scope);
    }).catch(function(error) {
      console.error('Service Worker registration failed:', error);
    });
  });
}
```

**Service Worker 文件（service-worker.js）**：

```javascript
// 安装阶段预缓存资源
self.addEventListener('install', function(event) {
  event.waitUntil(
    caches.open('my-cache').then(function(cache) {
      return cache.addAll([
        '/',
        '/index.html',
        '/styles.css',
        '/script.js',
        '/image.png'
      ]);
    })
  );
});

// 激活阶段清理旧缓存
self.addEventListener('activate', function(event) {
  event.waitUntil(
    caches.keys().then(function(cacheNames) {
      return Promise.all(
        cacheNames.filter(function(cacheName) {
          return cacheName !== 'my-cache';
        }).map(function(cacheName) {
          return caches.delete(cacheName);
        })
      );
    })
  );
});

// 拦截网络请求并应用缓存策略
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request).then(function(response) {
      return response || fetch(event.request).then(function(response) {
        return caches.open('my-cache').then(function(cache) {
          cache.put(event.request, response.clone());
          return response;
        });
      });
    })
  );
});
```

在使用 Service Workers 应该注意：

- Service Workers 只能在 HTTPS 环境下使用（localhost 除外），以确保数据传输的安全性。
- Service Workers 更新时需要先安装新的 Service Worker，然后等待旧的 Service Worker 停止活动，新的 Service Worker 才会接管控制权。
- 需要仔细管理 Service Workers 的生命周期，确保在安装、激活和更新过程中正确处理缓存和资源。
