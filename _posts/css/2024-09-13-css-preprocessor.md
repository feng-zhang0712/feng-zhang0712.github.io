---
layout: post
title: CSS 预处理器
categories: blog
tags:
  - css
  - sass
---

## 一、概念

CSS 预处理器是一种脚本语言，扩展了原生 CSS 的功能。通过预处理器，可以使用更高级的语法和特性来编写样式代码。预处理器文件（如 .sass）在编译过程中会被转换成标准的 CSS 文件。

## 二、常见的 CSS 预处理器

Sass：提供了两种语法，Sass（缩进语法）和 SCSS（类似 CSS 的语法）。Sass 是目前最流行的预处理器之一。
LESS：另一个流行的预处理器，语法上与 CSS 更接近，容易上手。

## 三、预处理器的用途和优点

### 3.1 变量

预处理器支持变量，可以用来存储重复使用的值（如颜色）等。

### 3.2 嵌套

预处理器允许嵌套 CSS，可以更清晰地表示层次结构。

### 3.3. 混合宏（Mixin）

混合宏允许定义一组可重用的样式规则，类似于函数。可以通过传递参数自定义样式。

```css
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
     -moz-border-radius: $radius;
          border-radius: $radius;
}

.button {
  @include border-radius(10px);
}

```

### 3.4 继承

预处理器支持继承样式，一个选择器可以继承另一个选择器的样式。

```css
%button-styles {
  padding: 10px 20px;
  border: none;
  cursor: pointer;
}

.button {
  @extend %button-styles;
  background-color: #3498db;
  color: #fff;
}

.button-secondary {
  @extend %button-styles;
  background-color: #2ecc71;
  color: #fff;
}
```

### 3.5 函数

预处理器允许定义函数，进行复杂的计算和逻辑操作，然后返回值，供样式规则使用。

```css
@function calculate-margin($value) {
  @return $value * 1.5;
}

.container {
  margin: calculate-margin(10px);
}
```

### 3.6 导入和拆分文件

预处理器支持将样式拆分成多个文件，并使用 @import 语句导入。

```css
/* _variables.scss */
$primary-color: #3498db;
$secondary-color: #2ecc71;

/* _mixins.scss */
@mixin flex-center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* styles.scss */
@import 'variables';
@import 'mixins';
.header {
  background-color: $primary-color;
  @include flex-center;
  height: 100vh;
}
```

### 3.7 条件语句和循环

预处理器支持条件语句和循环。

```css
$themes: ('light', 'dark');
@each $theme in $themes {
  .theme-#{$theme} {
    background-color: if($theme == 'light', #fff, #333);
    color: if($theme == 'light', #333, #fff);
  }
}
```