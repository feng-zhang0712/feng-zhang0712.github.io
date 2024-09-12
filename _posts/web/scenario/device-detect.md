# 应用访问设备判断

判断页面是通过哪种设备访问的，可以基于设备的特性（如操作系统、屏幕尺寸、User-Agent字符串等）进行检测。

## 一、使用 User-Agent 字符串

User-Agent 字符串包含了设备的详细信息。通过解析 User-Agent 字符串，可以判断设备类型。

```javascript
// 客户端实现
function getDeviceType() {
  const userAgent = navigator.userAgent || navigator.vendor || window.opera;

  // 移动设备
  if (/android|iphone|ipod|blackberry|iemobile|opera mini/i.test(userAgent)) {
    return 'Mobile';
  }

  // 平板设备
  if (/ipad|tablet|(android(?!.*mobile))/i.test(userAgent)) {
    return 'Tablet';
  }

  // 桌面设备
  return 'Desktop';
}

const deviceType = getDeviceType();
console.log(`Device type: ${deviceType}`);
```

```javascript
// 服务器端实现 (Node.js)
const http = require('http');

function getDeviceType(userAgent) {
  // 移动设备
  if (/android|iphone|ipod|blackberry|iemobile|opera mini/i.test(userAgent)) {
    return 'Mobile';
  }

  // 平板设备
  if (/ipad|tablet|(android(?!.*mobile))/i.test(userAgent)) {
    return 'Tablet';
  }

  // 桌面设备
  return 'Desktop';
}

http.createServer((req, res) => {
  const userAgent = req.headers['user-agent'];
  const deviceType = getDeviceType(userAgent);
  
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end(`Device type: ${deviceType}`);
}).listen(3000);

console.log('Server running at http://localhost:3000/');
```

## 二、使用 CSS 媒体查询

CSS 媒体查询可以根据设备特性（如屏幕宽度）来应用不同的样式，也可以用来判断设备类型。

```css
/* 移动设备样式 */
@media only screen and (max-width: 768px) {
  body::before {
    content: "Mobile device";
    display: block;
    text-align: center;
    font-size: 20px;
    color: red;
  }
}

/* 平板设备样式 */
@media only screen and (min-width: 769px) and (max-width: 1024px) {
  body::before {
    content: "Tablet device";
    display: block;
    text-align: center;
    font-size: 20px;
    color: blue;
  }
}

/* 桌面设备样式 */
@media only screen and (min-width: 1025px) {
  body::before {
    content: "Desktop device";
    display: block;
    text-align: center;
    font-size: 20px;
    color: green;
  }
}
```

```javascript
function getDeviceType() {
  if (window.matchMedia("only screen and (max-width: 768px)").matches) {
    return 'Mobile';
  }
  if (window.matchMedia("only screen and (min-width: 769px) and (max-width: 1024px)").matches) {
    return 'Tablet';
  }
  return 'Desktop';
}

const deviceType = getDeviceType();
console.log(`Device type: ${deviceType}`);
```

## 三、使用现代 JavaScript 库

使用现代 JavaScript 库（如 `Mobile-Detect`）可以更方便和准确地检测设备类型。

```javascript
// Node.js 环境
const MobileDetect = require('mobile-detect');
const md = new MobileDetect(navigator.userAgent);

let deviceType;
if (md.mobile()) {
  deviceType = 'Mobile';
} else if (md.tablet()) {
  deviceType = 'Tablet';
} else {
  deviceType = 'Desktop';
}

console.log(`Device type: ${deviceType}`);

// 浏览器环境
const md = new MobileDetect(window.navigator.userAgent);

let deviceType;
if (md.mobile()) {
  deviceType = 'Mobile';
} else if (md.tablet()) {
  deviceType = 'Tablet';
} else {
  deviceType = 'Desktop';
}

console.log(`Device type: ${deviceType}`);
```

## 四、综合方法

在实际项目中，可以综合使用多种方法来提高检测的准确性。例如，可以结合 User-Agent 字符串检测和 CSS 媒体查询来判断用户设备类型。

```javascript
function detectDevice() {
  const userAgent = navigator.userAgent || navigator.vendor || window.opera;
  const isMobile = /android|iphone|ipod|blackberry|iemobile|opera mini/i.test(userAgent);
  const isTablet = /ipad|tablet|(android(?!.*mobile))/i.test(userAgent);
  const isMobileMediaQuery = window.matchMedia("only screen and (max-width: 768px)").matches;
  const isTabletMediaQuery = window.matchMedia("only screen and (min-width: 769px) and (max-width: 1024px)").matches;

  if (isMobile || isMobileMediaQuery) {
    return 'Mobile';
  }
  if (isTablet || isTabletMediaQuery) {
    return 'Tablet';
  }
  return 'Desktop';
}

const deviceType = detectDevice();
console.log(`Device type: ${deviceType}`);
```
