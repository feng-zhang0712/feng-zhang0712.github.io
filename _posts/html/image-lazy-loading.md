## 图片懒加载

在前端开发中，实现图片懒加载可以显著提升页面的加载性能和用户体验。图片懒加载的基本想法是仅在用户即将看到图片时才进行加载，而不是在页面初始加载时立即加载所有图片。

### 一、使用HTML的`loading`属性

#### 1. 简介

现代浏览器（如Chrome、Firefox）提供了原生的`loading`属性，可以直接在`<img>`标签中使用`loading="lazy"`来实现懒加载。

#### 2. 使用方法

```html
<img src="example.jpg" alt="Example Image" loading="lazy">
```

#### 3. 优缺点

**优点**：

- **简单有效**：只需添加一个属性，便可实现懒加载。
- **浏览器支持**：不需要额外的JavaScript代码，依赖于浏览器的原生支持。

**缺点**：

- **兼容性问题**：并非所有浏览器都支持`loading`属性（需要检查兼容性）。
- **功能有限**：无法进行更多自定义操作。

### 二、使用JavaScript手动实现懒加载

#### 1. 简介

通过JavaScript，开发者可以手动实现懒加载功能，控制图片的加载时机。

#### 2. 使用方法

##### a. HTML部分

使用`data-src`属性来存储图片的真实URL，初始`src`属性使用占位符。

```html
<img data-src="example.jpg" alt="Example Image" class="lazy-load">
```

##### b. JavaScript部分

通过滚动事件监听器实现图片懒加载。

```javascript
document.addEventListener("DOMContentLoaded", function() {
  const lazyLoadImages = document.querySelectorAll("img.lazy-load");

  const lazyLoad = function() {
    lazyLoadImages.forEach(img => {
      if (img.getBoundingClientRect().top < window.innerHeight && img.getBoundingClientRect().bottom > 0) {
        img.src = img.getAttribute('data-src');
        img.classList.remove('lazy-load');
      }
    });

    if (lazyLoadImages.length === 0) {
      document.removeEventListener("scroll", lazyLoad);
      window.removeEventListener("resize", lazyLoad);
      window.removeEventListener("orientationchange", lazyLoad);
    }
  };

  document.addEventListener("scroll", lazyLoad);
  window.addEventListener("resize", lazyLoad);
  window.addEventListener("orientationchange", lazyLoad);
});
```

#### 3. 优缺点

**优点**：

- **兼容性好**：适用于所有支持JavaScript的浏览器。
- **高度自定义**：可以根据需要自定义实现方式和加载时机。

**缺点**：

- **复杂度高**：需要编写额外的JavaScript代码。
- **性能开销**：频繁的滚动事件监听可能会影响性能（可以考虑节流优化）。

### 三、使用IntersectionObserver API

#### 1. 简介

`IntersectionObserver` API 是一个现代的浏览器API，用于异步观察一个元素与其祖先元素或顶级文档视窗的交叉状态。它非常适合实现懒加载。

#### 2. 使用方法

##### a. HTML部分

同样使用`data-src`属性来存储图片的真实URL，初始`src`属性使用占位符。

```html
<img data-src="example.jpg" alt="Example Image" class="lazy-load">
```

##### b. JavaScript部分

使用`IntersectionObserver` API来实现图片懒加载。

```javascript
document.addEventListener("DOMContentLoaded", function() {
    const lazyLoadImages = document.querySelectorAll("img.lazy-load");

    if ("IntersectionObserver" in window) {
        const lazyLoad = function(entries, observer) {
            entries.forEach(entry => {
                if (entry.isIntersecting) {
                    const img = entry.target;
                    img.src = img.getAttribute('data-src');
                    img.classList.remove('lazy-load');
                    observer.unobserve(img);
                }
            });
        };

        const observer = new IntersectionObserver(lazyLoad, {
            root: null,
            rootMargin: "0px",
            threshold: 0.1
        });

        lazyLoadImages.forEach(img => {
            observer.observe(img);
        });
    } else {
        // Fallback for browsers that do not support IntersectionObserver
        const lazyLoad = function() {
            lazyLoadImages.forEach(img => {
                if (img.getBoundingClientRect().top < window.innerHeight && img.getBoundingClientRect().bottom > 0) {
                    img.src = img.getAttribute('data-src');
                    img.classList.remove('lazy-load');
                }
            });

            if (lazyLoadImages.length === 0) {
                document.removeEventListener("scroll", lazyLoad);
                window.removeEventListener("resize", lazyLoad);
                window.removeEventListener("orientationchange", lazyLoad);
            }
        };

        document.addEventListener("scroll", lazyLoad);
        window.addEventListener("resize", lazyLoad);
        window.addEventListener("orientationchange", lazyLoad);
    }
});
```

#### 3. 优缺点

**优点**：

- **高效性能**：`IntersectionObserver` API 是异步的，不会阻塞主线程，性能更好。
- **代码简洁**：比手动实现滚动监听的方式更简洁和高效。

**缺点**：

- **兼容性问题**：较旧的浏览器不支持`IntersectionObserver`（需要检查兼容性，或使用polyfill）。

### 四、其他方式

#### 1. 使用第三方库

##### a. 简介

有许多第三方库专门用于实现懒加载，如`lazysizes`、`lozad.js`等。这些库封装了复杂的懒加载逻辑，提供了简洁易用的接口。

##### b. 示例

使用`lazysizes`：

```html
<img data-src="example.jpg" alt="Example Image" class="lazyload">
```

引入`lazysizes`库：

```html
<script src="lazysizes.min.js" async></script>
```

#### 2. 优缺点

**优点**：

- **简便易用**：封装了复杂的逻辑，使用简单。
- **功能丰富**：通常提供了许多额外的功能，如预加载、延迟加载等。

**缺点**：

- **额外依赖**：引入了额外的依赖库，增加了项目的复杂性和体积。

### 五、总结

实现图片懒加载的方式有多种，选择适合的方式取决于具体的项目需求和浏览器兼容性：

1. **HTML的`loading`属性**：最简单的方式，适用于现代浏览器。
2. **JavaScript手动实现**：兼容性好，适合需要高度自定义的场景。
3. **IntersectionObserver API**：性能高效，代码简洁，适用于现代浏览器。
4. **第三方库**：功能丰富，使用简便，适合快速集成。
