---
layout: post
title: 响应式 Web 设计
description: 
categories: frontend
tags:
  - css
  - media queries
  - responsive web design
---

本文是对 [Ben Frain](https://benfrain.com/) 的《响应式 Web 设计：HTML5和CSS3实战 - 第二版》的读书摘要。

## 一、简介

响应式 Web 设计，指网页内容会随着访问它的视口及设备的不同而呈现不同的样式。W3C 对媒体查询下的定义是：媒体查询包含媒体类型和零个或多个检测媒体特性的表达式（比如，`width`、`height` 和 `color` 都是可用于媒体查询的特性）。使用媒体查询，可以不必修改内容本身，而让网页适配不同的设备。

在响应式设计的早期，人们会针对不同的流行的设备宽度设置**断点**，所谓“断点”，就是某个宽度临界点，跨过这个点布局就会发生显著变化。显然，这并不是一个好的做法，因为这样实际上把设计跟特定的屏幕大小绑定了。既然是响应式设计，那应该与显示它的设备无关才对，而不是只在某些屏幕中才最合适。断点应该由内容和设计本身决定，也就是，根据项目的实际需求设计决定。

## 二、媒体查询

**媒体查询**（Media Queries）主要用于设计响应式 Web 设计，使网页能够适应不同的设备和屏幕尺寸。即根据设备的特性（如屏幕宽度、高度、分辨率等）应用不同的样式。

常用媒体特性如下。

- `width`：视口的宽度。
- `height`：视口的高度。
- `device-width`：设备屏幕的宽度。
- `device-height`：设备屏幕的高度。
- `resolution`：设备屏幕的分辨率，通常以 dpi（每英寸点数）或 dppx（每像素点数）表示。
- `orientation`：设备的方向，可以是 `portrait` 或 `landscape`。
- `aspect-ratio`：视口的宽高比。不如，16∶9 的宽屏显示器可以写成 `aspect-ratio: 16/9`。
- `color`：设备的颜色位深。
- `color-index`：设备颜色查找表中的条目数，值必须是数值，且不能为负。
- `monochrome`：单色帧缓冲中表示每个像素的位数，值必须是数值（整数），比如 `monochrome: 2`，且不能为负。
- `scan`：针对电视的逐行扫描（progressive）和隔行扫描（interlace）。例如 720p HD TV（720p 中的 p 表示progressive，即逐行）可以使用 `scan: progressive` 来判断； 而 1080i HD TV（1080i 中的 i 表示 interlace，即隔行）可以使用 `scan: interlace` 来判断。
- `grid`：设备基于栅格还是位图。

上面列表中的特性，除 `grid` 外，都可以加上 `min-` 或 `max-` 前缀以指定范围。

### 2.1 媒体查询的语法

下面是一个最简单的媒体查询语法结构。

```css
@media media-type and (media-feature) {
  /* CSS规则 */
}
```

- `media-type`：指定设备类型，如 `screen`、`print` 等。可以省略，表示适用于所有类型（此时 `media-type: all`）。
- `media-feature`：指定的媒体特性，如 `max-width`、`min-width` 等。

也可以在 `<link>` 标签的 `media` 属性中指定设备类型。

```html
<link rel="style sheet" type="text/css" media="screen" href="style.css"> 
```

### 2.2 组合媒体查询

多个媒体查询组合，使用逗号（`,`）分隔不同的查询条件。表示如果任意一个条件为真则应用样式。还可以使用逻辑运算符 `and`、`or`、`not` 组合多个条件。

```css
/* 适用于屏幕最大宽度为600px或屏幕高度为 800px 的设备 */
@media screen and (max-width: 600px), screen and (max-height: 800px) {
  body {
    background-color: lightblue;
  }
}

/* 适用于屏幕宽度在 600px 到 1200px 之间且横向模式的设备 */
@media screen and (min-width: 600px) and (max-width: 1200px) and (orientation: landscape) {
  body {
    background-color: lightgreen;
  }
}
```

可以在使用 `@import` 导入 CSS 时使用媒体查询，有条件地向当前样式表中加载其他样式表。

```css
@import url("phone.css") screen and (max-width:360px); 
```

上面的代码表示，`phone.css` 样式表生效的条件是：设备类型必须是屏幕设备，且视口不超过 360px。

注意，在针对所有设备的媒体查询中，可以使用简写语法，即省略关键字 `all`（以及紧随其后的 `and`）。换句话说，如果不指定关键字，则关键字就是 `all`。

### 2.3 视口的 meta 标签

视口的 `meta` 标签，是网页与移动浏览器的接口。网页通过这个标签告诉移动浏览器，它希望浏览器如何渲染当前页面。视口的 `meta` 标签主要使用以下几个属性控制页面在移动端的行为。

- `width`：用于设置页面的宽度。
- `height`：用于设置页面的高度，通常设置了 `width` 就不会再设置 `height`。
- `initial-scale`：设置页面的初始缩放比例。
- `user-scalable`：一个布尔值，用于设置页面是否可以缩放。
- `minimum-scale`：设置页面的最小缩放比例，只有在 `user-scalable=true` 时，此属性才会生效。
- `maximum-scale`：设置页面的最大缩放比例，只有在 `user-scalable=true` 时，此属性才会生效。

```css
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, minimum-scale=0.5, maximum-scale=3" /> 
```

上面的代码表示，在移动设备上，页面的宽度为设备宽度，页面初始的缩放比例为 1，允许用户进行缩放操作，且最小和最大缩放比例分别为 0.5 和 3。

## 三、弹性布局与响应式图片

### 3.1 Flex 布局

### 3.2 Grid 布局

### 3.3 响应式图片

## 四、H5 与响应式 Web 设计

### 4.1 H5 页面结构新特性

（1）`doctype`：用于告诉浏览器文档类型。相比于 H4 的写法，新写法简化了很多。

```html
<!DOCTYPE html>
```

（2）`lang 属性`：用于指定文档使用的语言。
  
```html
<html lang="en">
```

（3）`charset`：`charset` 属性用于指定字符编码。除非有特殊需要，否则 `charset` 的属性值一般都是 `utf-8`。

```html
<meta charset="utf-8">
```

### 4.2 H5 结构化元素

（1）`<main>`：用于表示页面的主内容区。通常，每个页面的主内容区只能有一个，而且不能作为 `article`、`aside`、`header`、`footer`、`nav` 或 `header` 等其他 H5 语义元素的后代。

（2）`<section>`：用于定义文档或应用中一个通用的区块。比如，某块内容包含自然标题（h1 ~ h6），就可以使用 `<section>` 标签作为容器。

（3）`<nav>`：用于包装主导航链接。

（4）`<article>`：用于包含一个独立的内容块。比如，有博客正文和新闻报道就可以放在 `<article>` 中。

（5）`<aside>`：用于包含与其旁边内容不相关的内容。常用于侧边栏、突出引用、广告和导航元素。

（6）`<figure>` 和 `<figcaption>`：`<figure>` 常用于包含注解、图示、照片和代码等。`<figcaption>` 可作为 `<figure>` 的嵌套元素使用。

```html
<figure class="MoneyShot"> 
  <img class="MoneyShotImg" src="img/scones.jpg" alt="Incredible scones" /> 
  <figcaption class="ImageCaption">
    Incredible scones, picture from Wikipedia
  </figcaption> 
</figure> 
```

（7）`<detail>` 和 `<summary>`：这两个元素经常成对出现，比如，我们经常使用的“展开/收起”部件，就可以用它们来实现。

```html
<details> 
  <summary>I ate 15 scones in one day</summary> 
  <p>Of course I didn't. It would probably kill me if I did. What a way to go. Mmmmmm, scones!</p> 
</details>
```

在不添加任何样式的请款下，上面代码的（展开）效果如下。

![details and summary tag]({{ site.baseurl }}/assets/images/css/detail-and-summary.png)

（8）`<header>`：常用在站点页头作为“报头”。它可以在一个页面中出现多次（比如，页面中每个 `<section>` 中都可以有一个 `<header>`）。

（9）`<footer>`：常用于在相应区块中包含与区块相关的内容，可以包含指向其他文档的链接，或者版权声明。`<footer>` 同样可以在页面中出现多次。

（10）`<address>`：用于标记联系人信息，比如地址信息。

（11）`<h1>` ~ `<h6>`：h1 到 h6 元素不能用于标记副标题、字幕、广告语，除非想把它们用作新区块或子区块的标题。

### 4.3 H5 文本级元素

（1）`<b>`：表示只为引人注意而标记的文本，不传达更多的重要性信息，也不用于表达其他的愿望或情绪。比如，不用于文章摘要中的关键词、评测当中的产品名、交互式文本程序中的可执行命令，等等。

（2）`<em>`：表示内容中需要强调的部分。

（3）`<i>`：表示一段文本，用于表示另一种愿望或情绪，或者以突出不同文本形式的方式表达偏离正文的意思。

### 4.4 新的媒体元素

（1）`<video>`：用于在页面中嵌入视频，`<video>` 标签必须成对使用。如果浏览器不支持此标签，可以在其中潜入一段文字，用于提示。

```html
<video src="myVideo.mp4" width="640" height="480">
  What, do you mean you don't understand HTML5?
</video> 
```

对于某些旧版本浏览器，可以使用 `<source>` 标签以提供后备资源。比如，除了提供 MP4 版本的视频，如果想某些低版本浏览器也能看到视频，可以添加一个 Flash源 作为后备。或者，如果用户浏览器没有任何播放条件，还可以提供一个下载视频的链接。

```html
<video width="640" height="480" controls preload="auto" loop poster="myVideoPoster.png">
  <source src="video/myVideo.mp4" type="video/mp4"> 
  <object
    width="640"
    height="480"
    type="application/x-shockwaveflash"
    data="myFlashVideo.SWF"
  >
    <param name="movie" value="myFlashVideo.swf" /> 
    <param name="flashvars" value="controlbar=over&amp;image=myVideoPoster.jpg&amp;file=myVideo.mp4" /> 
    <img
      src="myVideoPoster.png"
      width="640"
      height="480"
      alt="__ TITLE__"
      title="No video playback capabilities, please download the video below"
    /> 
  </object> 
  <p>
    <b>Download Video:</b> MP4 Format: <a href="myVideo.mp4">"MP4"</a> 
  </p> 
</video>
```

通常，很难对不同的视频播放画面做响应式设计，多数情况下，影片的播放比例并不相同。[这里](http://embedresponsively.com/) 有一个网站，你可以把视频的 url 地址粘贴进去，就会得到一段响应式代码，从而使得在网页缩放时，视频依然会保持应有的比例。

（2）`<audio>`：`<audio>` 用于在页面中嵌入音频，其用法与 `<video>` 类似。

### 4.5 其他特性

（1）`<a>` 标签中可以放多个元素。

```html
<a href="index.html"> 
  <h2>The home page</h2> 
  <p>This paragraph also links to the home page</p> 
  <img src="home-image.png" alt="home-slice" /> 
</a> 
```

（2）WCAG 和 WAI-ARIA

WCAG 和 WAI-ARIA 提供了与无障碍交互相关的标准。

## 五、CSS3 新特性

这部分并不会讨论 CSS3 中所有的新特性，而只是讨论跟响应式设计有关的新特性。

### 5.1 CSS 响应式多列布局

以下示例，以下面的代码布局为例进行讲解。

```html
<main> 
  <p>lloremipsimLoremipsum dolor sit amet, consectetur ... </p> 
  <p>lloremipsimLoremipsum dolor sit amet, consectetur ... </p> 
</main>
```

（1）使用 CSS 多列布局可以通过几种方式让文本分成多列显示。比如，可以给每列设定固定的列宽。

```css
main { 
  column-width: 12em; 
}
```

上面的代码表示，设置没列的宽度为 12em，改变视口宽度时，列宽不变，列数动态改变。

（2）固定列数，可变宽度。

```css
main { 
  column-count: 4; 
}
```

上面的代码表示，当页面缩放时，列数固定，宽度可变。

（3）添加列间距和分隔线。

```css
main { 
  column-gap: 2em; 
  column-rule: thin dotted #999; 
  column-width: 12em; 
}
```

上面的代码，用于给列间添加间距和分隔线。

### 5.2 断字

（1）容器中的问题，默认一行显示，如果蚊子太长，超出了容器的长度，就会显示在容器外面。可以是同下面的样式，对文字进行**换行**处理。

```css
word-wrap: break-word;
```

（2）可以对文字**截短**处理，并且使得超出容器宽度的部分，显示为 `...`。

```css
p { 
 width: 520px; 
 overflow: hidden; 
 text-overflow: ellipsis; 
 white-space: no-wrap; 
} 
```

最后的 `white-space: nowrap` 声明是为了确保长出来的文本不会折行显示在外部元素中。

### 5.3 在 CSS 中创建分支

在响应式设计，经常会碰到某些设备不支持什么特性或技术的情况。此时，往往需要在 CSS 中创建一个分支。如果浏览器支持某特性，就应用一段代码；如果不支持，则应用另一段代码。这点类似于 JavaScript 中 `if...else` 语句。

在 CSS 中创建分支有两种手段。一是完全基于 CSS，但支持的浏览器却不多； 二是借助 JavaScript 库，获得广泛兼容性。

（1）特性查询

CSS 原生的分支语法就是特性查询，比如下面的代码。

```css
@supports (display: flex) { 
  .item { 
    display: inline-flex; 
  } 
}

@supports not (display: flex) { 
  .item { 
    display: inline-block; 
  } 
} 
```

上面的代码表示，如果浏览器支持 `display: flex`，就对 `.item` 选择器设置一种样式；否则，则设置为另一种样式。

（2）组合条件

假设我们只想在浏览器支持 flexbox 和 `pointer: coarse` 时应用某些规则，可以使用下面的代码。

```css
@supports ((display: flex) and (pointer: coarse)) { 
  .item { 
    display: inline-flex; 
  } 
} 
```

上面的代码，使用 `and` 关键字，其他支持的关键字还有 `or`。比如，除了前面两个条件满足之外，如果浏览器支持 3D 变形也想应用样式，那么可以使用下面的代码。

```css
@supports ((display: flex) and (pointer: coarse)) or (transform: translate3d(0, 0, 0)) { 
  .item { 
    display: inline-flex; 
  } 
} 
```

注意，某些低版本及 IE 浏览器，并不支持 `@support` 关键字。

（3）在 `@supports` 得到广泛支持以前，还可以使用 Modernizr 这个 JavaScript 工具在 CSS 中实现分支。

### 5.4 新的 CSS3 选择器

（1）属性选择器，用于对某些元素的属性进行选择。

```css
img[alt] { 
  border: 3px dashed #e15f5f; 
} 
```

上面的代码，表示选中任何包含 `alt` 属性的 `<img>` 元素。

```css
img[alt="sausages"] { 
  /* 样式 */ 
}
```

上面的代码，只会选择 `alt` 属性值为 `sausages` 的 `<img>`元素。即同时指定了属性的值，进一步缩小了搜索范围。

CSS3 支持依据属性选择器包含的子字符串来选择元素。这时分为三种情况：

- 以 xxx 开头。
- 包含 xxx。
- 以 xxx 结尾。

（2）选中属性值以某字符串开头的元素。

```html
<img src="img/ace-film.jpg" alt="film-ace"> 
<img src="img/rubbish-film.jpg" alt="film-rubbish"> 
```

```css
img[alt^="film"] { 
  /* 样式 */ 
} 
```

上面的代码，使用 `^` 符号，匹配 `alt` 属性值中以 `film` 字符串开头的情况。即此时会选中 `<img>` 表情中，`alt` 属性以 `film` 开头的元素（在这里会同时选中上边的两个 `<img>` 标签）。

（2）选中属性值包含某字符串的元素。

```html
<p data-ingredients="scones cream jam">Will I get selected?</p>
```

```css
[data-ingredients*="cream"] { 
  color: red; 
} 
```

上面的属性选择器，使用 `*` 符号，匹配 `data-ingredients` 属性值中包含 `cream` 字符串的情况。此时 `<p>` 标签会被选中。

（3）选中属性值以某字符串结尾的元素。

```html
<p data-ingredients="scones cream jam">Will I get selected?</p> 
<p data-ingredients="toast jam butter">Will I get selected?</p> 
<p data-ingredients="jam toast butter">Will I get selected?</p> 
```

```css
[data-ingredients$="jam"] { 
  color: red; 
} 
```

上面的属性选择器，使用 `$` 符号，匹配 `data-ingredients` 属性值中以 `jam` 字符串结尾的情况。此时，第一个 `<p>` 标签会被选中。

### 5.5 伪类选择器

- `:last-child`：用于选择某个父元素中的最后一个子元素。
- `:nth-child(n)`：
- `:nth-last-child(n)`：
- `:nth-of-type(n)`：
- `:nth-last-of-type(n)`：
- `:not`：
- `:empty`：
- `:first-line`：
- `:has`：

### 5.6 `calc()` 函数

### 5.7 CSS3 的新颜色格式及透明度

（1）可以使用 `rgb()` 或 `rgba()` 函数定义颜色，前者接收三个参数（取值为 0 ~ 255），分别表示红、绿、蓝三原色分量的值，后者除了这三个参数，还可以接受一个 alpha 透明度数值，取值为 0 ~ 1。

（2）CSS3 还支持HSL（Hue Saturation Lightness，色相、饱和度、亮度）颜色系统。

## 六、CSS3 高级技术

## 七、SVG 与响应式 Web 设计

## 八、CSS3 过渡、变形和动画

## 九、表单