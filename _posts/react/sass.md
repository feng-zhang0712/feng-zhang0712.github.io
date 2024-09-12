# Sass

## 一、什么是 Sass

**Sass** 是一种强大的 CSS 扩展语言，提供了更高级的功能和更简洁的语法，使 CSS 的编写和维护更加高效。Sass 提供了 变量（variables）、嵌套规则（nested rules）、 混合（mixins）、 函数（functions） 等功能。

## 二、两种语法

Sass 提供了两种语法格式，分别是 SCSS (Sassy CSS) 和缩进语法 (Indented Syntax)，两种语法格式在功能和特性上完全一致，只是语法风格不同。

### 2.1 Scss

- **简介**：SCSS 是 Sass 的一种语法格式，它完全兼容 CSS3，因此所有合法的 CSS 语法在 SCSS 中也同样适用。SCSS 通过引入 Sass 的高级特性如变量、嵌套、混合等，增强了 CSS 的功能。
- **文件扩展名**：`.scss`
- **语法特点**：与传统的 CSS 相似，使用大括号 `{}` 表示代码块，用分号 `;` 分隔属性。

下面的代码，是一个使用 SCSS 语法格式的例子。

```scss
$primary-color: #3498db;
$font-stack: Helvetica, sans-serif;

body {
  font: 100% $font-stack;
  color: $primary-color;
}

nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }
}
```

### 2.2 缩进语法 (Indented Syntax)

- **简介**：缩进语法是 Sass 的另一种语法格式，它使用缩进来表示嵌套关系，而不是大括号和分号。这种语法格式更简洁，但需要严格的缩进规则。
- **文件扩展名**：`.sass`
- **语法特点**：使用缩进表示层次结构，不需要大括号和分号。

下面的代码，是一个使用缩进语法格式的例子。

```sass
$primary-color: #3498db
$font-stack: Helvetica, sans-serif

body
  font: 100% $font-stack
  color: $primary-color

nav
  ul
    margin: 0
    padding: 0
    list-style: none

  li
    display: inline-block
```

## 三、Sass 的特性

### 3.1 变量

#### （1）变量的定义和使用

Sass 允许使用变量来存储重复使用的值，如颜色、字体大小等。

在 Sass 中，定义变量的语法非常简单。变量名以美元符号 `$` 开头，后面跟随变量的值。

```scss
$primary-color: #3498db;
$font-stack: Helvetica, sans-serif;
```

定义变量后，可以在样式规则中使用这些变量，替代具体的值。

```scss
body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

#### （2）变量的作用域

Sass 中的变量作用域可以是全局的或局部的。

在顶层定义的变量是全局变量，整个样式表文件中都可以访问。

```scss
$primary-color: #3498db;

body {
  color: $primary-color;
}

header {
  background-color: $primary-color;
}
```

在代码块内（如函数、混合、选择器）定义的变量是局部变量，只能在该代码块内访问。

```scss
body {
  $secondary-color: #2ecc71;
  color: $secondary-color;
}
```

#### （3）默认变量

Sass 提供了一个特殊的 `!default` 标志，允许你定义一个默认值的变量。如果该变量已经被赋值，则不会覆盖现有值。

```scss
$primary-color: red !default;

body {
  color: $primary-color; // 如果 $primary-color 已经定义，则使用已有值，否则使用红色
}
```

#### （4）变量插值

使用变量插值可以构建复杂的选择器或属性值。插值使用 `#{}` 语法。

```scss
$module: 'footer';
.#{$module}-content {
  color: blue;
}
```

#### （5）变量的计算

Sass 允许使用变量进行动态计算，生成新的值。

```scss
$base-font-size: 16px;
$line-height: 1.5;
$base-line-height: $base-font-size * $line-height;

body {
  font-size: $base-font-size;
  line-height: $base-line-height;
}
```

### 3.2 嵌套

嵌套允许在选择器内嵌套其他选择器和样式规则，从而使代码更具层次结构，简洁明了。嵌套不仅能应用于选择器，还可以用于属性、伪类、媒体查询等。

#### （1）选择器嵌套

选择器嵌套是 Sass 最常用的嵌套方式，允许将子选择器嵌套在父选择器内部，使样式层次更加清晰。

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }
}
```

父选择器引用符号 `&` 用于在嵌套规则中引用父选择器。特别适用于伪类（如 `:hover`, `:before`, `:after`）和伪元素。

```scss
button {
  display: inline-block;
  padding: 10px 20px;
  background-color: #3498db;
  border: none;
  color: white;

  &:hover {
    background-color: #2980b9;
  }

  &:before {
    content: '▶';
    margin-right: 5px;
  }
}
```

属性嵌套是 Sass 提供的另一个强大特性，允许你将相关的属性嵌套在一起，简化代码。

```scss
.box {
  border: {
    style: solid;
    width: 1px;
    color: #333;
  }

  font: {
    family: Arial, sans-serif;
    size: 16px;
    weight: bold;
  }
}
```

#### （2）媒体查询嵌套

Sass 支持在选择器内部嵌套媒体查询，使响应式设计更加直观。

```scss
.container {
  width: 100%;
  @media (min-width: 768px) {
    width: 750px;
  }
  @media (min-width: 992px) {
    width: 970px;
  }
  @media (min-width: 1200px) {
    width: 1170px;
  }
}
```

### 3.3 混合（Mixin）

混合（Mixin）允许开发者定义可重用的样式块，并在需要的地方调用。通过使用 Mixin，可以减少代码的重复，提高样式的可维护性和模块化程度。

#### （1）定义 Mixin

在 Sass 中，可以使用 `@mixin` 指令来定义一个 Mixin。Mixin 可以包含任何 CSS 规则，并且可以接受参数以实现更灵活的样式复用。

```scss
@mixin border-radius {
  -webkit-border-radius: 10px;
     -moz-border-radius: 10px;
      -ms-border-radius: 10px;
          border-radius: 10px;
}
```

#### （2）使用 Mixin

使用 `@include` 指令来调用已经定义的 Mixin。

```scss
.box {
  @include border-radius;
}
```

#### （3）Mixin 参数

Mixin 可以接受参数，使其更灵活。参数可以有默认值，也可以在调用时进行传递。

```scss
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
     -moz-border-radius: $radius;
      -ms-border-radius: $radius;
          border-radius: $radius;
}

.box {
  @include border-radius(15px);  // 调用时传递参数
}
```

#### （4）默认参数

可以为 Mixin 定义默认参数，当调用时未传递相应的参数时，使用默认值。

```scss
@mixin box-shadow($shadow: 0 0 5px rgba(0, 0, 0, 0.3)) {
  -webkit-box-shadow: $shadow;
     -moz-box-shadow: $shadow;
      -ms-box-shadow: $shadow;
          box-shadow: $shadow;
}

.card {
  @include box-shadow;  // 使用默认参数
  @include box-shadow(0 0 10px rgba(0, 0, 0, 0.5));  // 传递新参数
}
```

#### （5）Mixin 传递多个参数

Mixin 可以接受多个参数，参数之间用逗号分隔。

```scss
@mixin size($width, $height) {
  width: $width;
  height: $height;
}

.rectangle {
  @include size(100px, 200px);
}
```

#### （4）可变参数（Variable Arguments）

Sass 支持可变参数，使用 `...` 表示，可以传递任意数量的参数。

```scss
@mixin colors($color1, $color2: null, $color3: null) {
  background-color: $color1;
  @if $color2 {
    border-color: $color2;
  }
  @if $color3 {
    color: $color3;
  }
}

.button {
  @include colors(#ff0000);  // 仅一个参数
  @include colors(#ff0000, #00ff00);  // 两个参数
  @include colors(#ff0000, #00ff00, #0000ff);  // 三个参数
}
```

#### （7）内容块（Content Blocks）

Mixin 还可以包含内容块，允许在调用 Mixin 时传递代码块。

```scss
@mixin apply-to-ie6 {
  * html {
    @content;
  }
}

@include apply-to-ie6 {
  #logo {
    background-image: url(/logo.gif);
  }
}
```

### 3.4 继承

Sass 的继承功能允许一个选择器继承另一个选择器的样式，这个功能通过 `@extend` 指令实现。

#### （1）继承的基本使用

使用 `@extend` 指令可以让一个选择器继承另一个选择器的所有样式。被继承的选择器可以是任何 CSS 选择器，包括类、ID、标签等。

```scss
.button {
  padding: 10px 20px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
}

.primary-button {
  @extend .button;
  background-color: #2980b9;
}
```

#### （2）占位符选择器

占位符选择器（Placeholder Selector）是一种特殊的选择器，用于定义可继承的样式而不会在 CSS 输出中生成任何样式。占位符选择器以 `%` 符号开头。

```scss
%button-base {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
}

.primary-button {
  @extend %button-base;
  background-color: #3498db;
  color: white;
}

.secondary-button {
  @extend %button-base;
  background-color: #2ecc71;
  color: white;
}
```

#### （3）嵌套继承

继承也可以在嵌套的选择器中使用，这样可以保持样式的层次结构。

```scss
.container {
  %box {
    padding: 20px;
    border: 1px solid #ddd;
  }

  .card {
    @extend %box;
    background-color: #f5f5f5;
  }

  .alert {
    @extend %box;
    background-color: #ffe5e5;
    border-color: #ff0000;
  }
}
```

### 3.5 functions

Sass 提供了强大的函数功能，允许开发者定义自己的函数来处理和返回值，从而使样式表更具灵活性和可维护性。除了自定义函数，Sass 还内置了许多有用的函数来执行颜色操作、字符串操作、数字计算等。

#### （1）定义和使用自定义函数

使用 `@function` 指令定义一个自定义函数，函数名以 `$` 符号开头，并且可以接受参数。函数体内使用 `@return` 指令返回结果。

```scss
@function calculate-percentage($partial, $total) {
  @return ($partial / $total) * 100%;
}
```

在样式规则中调用自定义函数，与使用 Sass 内置函数的方式相同。

```scss
$content-width: 800px;
$sidebar-width: 300px;

.main-content {
  width: calculate-percentage($content-width, $content-width + $sidebar-width);
}
```

#### （2）Sass 内置函数

Sass 提供了丰富的内置函数，涵盖了颜色、字符串、数字、列表、地图等多种操作。

颜色函数用于处理颜色值，包括调整颜色亮度、透明度等。

```scss
// SCSS 语法
$base-color: #3498db;

.lighten-color {
  color: lighten($base-color, 20%);
}

.darken-color {
  color: darken($base-color, 20%);
}

.opacity-color {
  color: rgba($base-color, 0.5);
}
```

常用颜色函数：

- `lighten($color, $amount)`: 增加颜色亮度
- `darken($color, $amount)`: 减少颜色亮度
- `rgba($color, $alpha)`: 设置颜色透明度
- `mix($color1, $color2, $weight)`: 混合两种颜色

字符串函数用于处理字符串，包括拼接、获取子字符串等。

```scss
$font-family: "Helvetica";

.font-style {
  font-family: quote($font-family + ", Arial, sans-serif");
}

.text-transform {
  content: to-upper-case("hello world");
}
```

常用字符串函数：

- `quote($string)`: 给字符串添加引号
- `unquote($string)`: 移除字符串的引号
- `to-upper-case($string)`: 将字符串转为大写
- `to-lower-case($string)`: 将字符串转为小写

数字函数用于处理数值，包括取整、取绝对值等。

```scss
$value: -42.7;

.absolute-value {
  content: abs($value);
}

.round-value {
  content: round($value);
}

.ceil-value {
  content: ceil($value);
}

.floor-value {
  content: floor($value);
}
```

常用数字函数：

- `abs($number)`: 返回绝对值
- `round($number)`: 对数值进行四舍五入
- `ceil($number)`: 向上取整
- `floor($number)`: 向下取整

##### 列表函数

列表函数用于处理列表，包括获取列表长度、访问列表元素等。

```scss
$list: 1px solid red;

.list-length {
  content: length($list);
}

.list-nth {
  content: nth($list, 2);
}

.list-append {
  content: append($list, blue);
}
```

常用列表函数：

- `length($list)`: 返回列表长度
- `nth($list, $n)`: 获取列表中的第 n 个元素
- `append($list, $value, $separator: auto)`: 在列表末尾添加元素
- `join($list1, $list2, $separator: auto)`: 合并两个列表

##### 地图函数

地图函数用于处理键值对映射，包括获取键、值、合并地图等。

```scss
$map: (primary: #3498db, secondary: #2ecc71);

.map-get {
  content: map-get($map, primary);
}

.map-keys {
  content: map-keys($map);
}

.map-values {
  content: map-values($map);
}

.map-merge {
  content: map-merge($map, (tertiary: #e74c3c));
}
```

常用地图函数：

- `map-get($map, $key)`: 获取地图中指定键的值
- `map-keys($map)`: 获取地图中的所有键
- `map-values($map)`: 获取地图中的所有值
- `map-merge($map1, $map2)`: 合并两个地图

### 3.6 部件（Partials）和导入（Import）

Sass 允许将样式分成多个文件，通过 `@import` 语句组合在一起。

```scss
// _reset.scss
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

// styles.scss
@import 'reset';

body {
  font-family: Arial, sans-serif;
}
```

### 3.7 条件和循环

Sass 提供条件语句和循环结构，使样式更具动态性。

```scss
@mixin theme($theme-name) {
  @if $theme-name == dark {
    background-color: #333;
    color: #eee;
  } @else if $theme-name == light {
    background-color: #fff;
    color: #333;
  } @else {
    background-color: #f5f5f5;
    color: #333;
  }
}

body { @include theme(dark); }

@for $i from 1 through 3 {
  .m-#{$i} {
    margin: $i * 10px;
  }
}
```

参考

- [Sass](https://sass-lang.com/)
