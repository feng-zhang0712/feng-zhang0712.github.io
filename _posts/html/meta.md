`<meta>` 标签是 HTML 中的一种用于提供关于 HTML 文档的元数据（数据的数据）的标签。元数据不会显示在网页上，但它们对搜索引擎、浏览器和其他网络服务非常重要。

### 基本格式

```html
<meta name="name" content="content">
<meta http-equiv="http-equiv" content="content">
<meta charset="charset">
<meta property="property" content="content">
```

### 常见的用途

1. **字符集声明**

   确定 HTML 文档的字符编码。这是最常见的 `<meta>` 标签之一，通常放在文档的头部。

   ```html
   <meta charset="UTF-8">
   ```

2. **描述和关键词**

   提供页面内容的简短描述和关键词，有助于搜索引擎优化（SEO）。

   ```html
   <meta name="description" content="这是一个关于HTML meta标签的详细介绍">
   <meta name="keywords" content="HTML, meta标签, SEO, Web开发">
   ```

3. **视口设置**

   配置移动设备上网页的显示方式，尤其是响应式设计中常用。

   ```html
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   ```

4. **刷新或重定向**

   使用 `http-equiv` 属性来指定页面在一定时间后刷新或重定向。

   ```html
   <meta http-equiv="refresh" content="30">
   <!-- 页面在30秒后刷新 -->
   <meta http-equiv="refresh" content="5; url=https://www.example.com">
   <!-- 页面在5秒后重定向到指定URL -->
   ```

5. **作者和版权信息**

   提供文档的作者和版权信息。

   ```html
   <meta name="author" content="张锋">
   <meta name="copyright" content="© 2024 Example Company">
   ```

6. **兼容性视图**

   指定某些浏览器（如 Internet Explorer）的兼容性模式。

   ```html
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   ```

7. **防止页面缓存**

   控制浏览器和代理服务器的缓存行为。

   ```html
   <meta http-equiv="cache-control" content="no-cache">
   <meta http-equiv="pragma" content="no-cache">
   ```

8. **Open Graph 协议**

   用于社交媒体（如 Facebook）分享时的显示信息。

   ```html
   <meta property="og:title" content="HTML Meta 标签介绍">
   <meta property="og:description" content="详细介绍HTML中的meta标签及其用途">
   <meta property="og:image" content="https://www.example.com/image.jpg">
   <meta property="og:url" content="https://www.example.com/meta-tags">
   ```

9. **Twitter 卡片**

   为在 Twitter 上分享的内容提供优化的显示信息。

   ```html
   <meta name="twitter:card" content="summary_large_image">
   <meta name="twitter:title" content="HTML Meta 标签介绍">
   <meta name="twitter:description" content="详细介绍HTML中的meta标签及其用途">
   <meta name="twitter:image" content="https://www.example.com/image.jpg">
   ```

10. **主题颜色**

    为移动设备上的浏览器和操作系统提供主题颜色。

    ```html
    <meta name="theme-color" content="#ffffff">
    ```

### `http-equiv` 属性

`<meta>` 标签的 `http-equiv` 属性用于在 HTML 文档中模拟 HTTP 响应头字段，影响浏览器的行为。通过合理使用这些属性，你可以控制页面的缓存行为、字符编码、语言设置、刷新和重定向等。

#### 常见的 `http-equiv` 值及其用途

1. **Content-Type**

   指定文档的 MIME 类型和字符编码。虽然现代 HTML 通常使用 `<meta charset="UTF-8">`，但 `Content-Type` 仍然在某些情况下使用。

   ```html
   <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
   ```

2. **default-style**

   设置文档的默认样式表。

   ```html
   <meta http-equiv="default-style" content="stylesheet_name">
   ```

3. **refresh**

   设置页面自动刷新或重定向。`content` 属性的值为 "秒; URL" 格式。

   ```html
   <meta http-equiv="refresh" content="30">
   <!-- 每30秒刷新一次页面 -->
   <meta http-equiv="refresh" content="5; url=https://www.example.com">
   <!-- 5秒后重定向到指定的URL -->
   ```

4. **X-UA-Compatible**

   指定页面在特定版本的 Internet Explorer 中的渲染方式，通常用于强制 IE 使用最新的渲染引擎。

   ```html
   <meta http-equiv="X-UA-Compatible" content="IE=edge">
   ```

5. **Content-Language**

   指定文档的语言。

   ```html
   <meta http-equiv="Content-Language" content="en">
   ```

6. **cache-control**

   控制浏览器和代理服务器的缓存行为。

   ```html
   <meta http-equiv="cache-control" content="no-cache">
   ```

7. **expires**

   指定文档的过期时间，格式为 HTTP 日期。

   ```html
   <meta http-equiv="expires" content="Wed, 21 Oct 2024 07:28:00 GMT">
   ```

8. **pragma**

   提供缓存控制信息，通常用于禁止浏览器缓存页面。

   ```html
   <meta http-equiv="pragma" content="no-cache">
   ```

9. **set-cookie**

   设置 cookie 信息。该属性实际上很少使用，因为通常通过服务器端设置 cookie。

   ```html
   <meta http-equiv="set-cookie" content="name=value; expires=Wed, 21 Oct 2024 07:28:00 GMT">
   ```

### 其他用途

- **验证网站所有权**

  一些搜索引擎和网络服务要求你在 HTML 文档中添加特定的 `<meta>` 标签来验证网站所有权。

  ```html
  <meta name="google-site-verification" content="verification_token">
  ```

- **内容类型和语言**

  指定文档的内容类型和语言。

  ```html
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <meta http-equiv="Content-Language" content="en">
  ```

### 示例文档

以下是一个包含多种 `<meta>` 标签的示例 HTML 文档：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="这是一个关于HTML meta标签的详细介绍">
  <meta name="keywords" content="HTML, meta标签, SEO, Web开发">
  <meta name="author" content="张锋">
  <meta name="copyright" content="© 2024 Example Company">
  <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
  <meta http-equiv="refresh" content="30">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta http-equiv="Content-Language" content="en">
  <meta http-equiv="cache-control" content="no-cache">
  <meta http-equiv="pragma" content="no-cache">
  <meta property="og:title" content="HTML Meta 标签介绍">
  <meta property="og:description" content="详细介绍HTML中的meta标签及其用途">
  <meta property="og:image" content="https://www.example.com/image.jpg">
  <meta property="og:url" content="https://www.example.com/meta-tags">
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="HTML Meta 标签介绍">
  <meta name="twitter:description" content="详细介绍HTML中的meta标签及其用途">
  <meta name="twitter:image" content="https://www.example.com/image.jpg">
  <meta name="theme-color" content="#ffffff">
  <title>HTML Meta 标签介绍</title>
</head>
<body>
  <h1>欢迎来到HTML Meta标签介绍页面</h1>
  <p>本文详细介绍了HTML中的meta标签及其各种用途。</p>
</body>
</html>
```

### 总结

`<meta>` 标签在 HTML 文档中扮演着重要角色，通过提供关于文档的额外信息来改善网页的 SEO、可访问性和用户体验。了解和使用这些标签可以帮助你更好地控制网页在搜索引擎、浏览器和社交媒体中的表现。

参考

- [\<meta\>：元数据元素](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta)