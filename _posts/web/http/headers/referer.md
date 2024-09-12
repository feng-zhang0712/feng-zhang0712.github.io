# Referer

Referer 字段是 HTTP 请求头的一部分，用于指示当前请求的来源页面。Referer 字段在网络请求中的多个场景中发挥重要作用，包括分析流量、反垃圾邮件和安全控制等。

Referer 字段的作用

1. 追踪流量来源
Referer 字段可以用来追踪用户是如何到达当前页面的。例如，如果用户点击了一个外部网站的链接，目标网站的服务器可以通过 Referer 头部知道用户的来源。
2. 分析和统计
Referer 字段 可用于流量分析，了解用户的访问路径和行为，进而优化网站结构和内容。
3. 防范 CSRF 攻击
Referer 字段可用于防范跨站请求伪造（CSRF）攻击，通过验证请求的来源，确保请求从同一站点发起。
注意，这种方法并不完全可靠，因为 Referer 头部可以被伪造或被某些防火墙和代理服务器删除。
4. 反垃圾邮件
在表单提交和评论系统中，通过验证 Referer 字段，可以阻止来自恶意网站的垃圾提交。
Referer 字段的工作原理
当浏览器向服务器发送请求时，Referer 字段会包含当前请求的来源 URL。例如，当用户从 <http://example.com/page> 点击一个链接到 <http://another-site.com/resource，浏览器会发送如下请求：>
GET /resource HTTP/1.1
Host: another-site.com
Referer: <http://example.com/page>

服务器可以通过 Referer 头部获取到用户的来源页面 URL。

1. 在前端代码中查看 Referer 字段
可以通过 JavaScript 获取当前页面的 document.referrer 属性来查看来源页面：
console.log(document.referrer);

2. 在服务器端验证 Referer 字段
服务器可以在处理请求时验证 Referer 字段，确保请求来自合法的来源。
Referer 字段的隐私和安全考虑
1. Referer 头泄露隐私
Referer 头部可能会泄露用户的敏感信息。如果 URL 中包含敏感数据（如查询参数中的个人信息），这些信息可能会通过 Referer 头部泄露给其他网站。
比如，用户从包含敏感信息的页面访问另一个网站：
GET /resource HTTP/1.1
Host: another-site.com
Referer: <http://example.com/page?query=sensitive-data>

2. 使用 Referrer-Policy 控制 Referer 头
通过 Referrer-Policy 头部或 HTML 元素的 referrerpolicy 属性来控制 Referer 的行为，防止敏感信息泄露。
Referrer-Policy: no-referrer

Referrer-Policy 选项
no-referrer：不发送 Referer 信息。
no-referrer-when-downgrade：如果请求从 HTTPS 页面向 HTTP 页面发送，则不发送 Referer 信息（默认策略）。
origin：仅发送请求的源（协议、主机和端口），例如 <https://example.com。>
origin-when-cross-origin：对同源请求发送完整的 URL，对跨源请求仅发送源。
same-origin：仅对同源请求发送 Referer 信息。
strict-origin：仅发送源，但在降级请求（HTTPS 到 HTTP）时不发送。
strict-origin-when-cross-origin：同源请求发送完整 URL，跨源请求发送源，在降级请求时不发送。
unsafe-url：始终发送完整的 URL，包括 HTTPS 到 HTTP 的降级请求。

可以在链接、表单或脚本等 HTML 元素中使用 referrerpolicy 属性设置策略：
<a href="https://another-site.com/resource" referrerpolicy="no-referrer">Link</a>
<form action="https://another-site.com/submit" referrerpolicy="origin">
  <!-- 表单内容 -->
</form>

Referer 字段与安全性

1. 防范 CSRF 攻击
通过验证 Referer 头部可以防范 CSRF 攻击，确保请求来自合法的来源。
注意，这种方法并不完全可靠，因为 Referer 头部可以被伪造或被某些防火墙和代理服务器删除。
2. 防范点击劫持
在防范点击劫持攻击时，X-Frame-Options 和 Content-Security-Policy 的 frame-ancestors 指令比 Referer 头部更有效。
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'self'

Referer 字段在前端开发中具有重要作用，包括追踪流量来源、分析和统计、防范 CSRF 攻击和反垃圾邮件等。然而，Referer 头部可能会泄露用户的敏感信息，因此开发者应合理使用 Referrer-Policy 头部或 HTML 元素的 referrerpolicy 属性来控制 Referer 头部的行为，确保用户隐私和数据的安全。在防范 CSRF 攻击时，验证 Referer 头部是常见的方法之一，但结合使用其他安全技术（如 CSRF Token）效果更佳。
