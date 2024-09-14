---
layout: post
title: CSS 动画
description: this is how you can display code diffs
categories: css
tags:
  - css
  - animation
---

CSS 中与动画有关的模块主要有三个：CSS Transforms、CSS Transitions 以及 Animation 动画。下面，依次对他们进行讲解。

## 一、Transform 动画

### 1.1 介绍

[CSS Transforms](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_transforms) 是一个 CSS 模块，它定义了如何在二维或三维空间中转换元素的 CSS 样式。Transform 动画是一系列动画效果的集合，比如平移（translate）、旋转（rotate）、缩放（scale）和倾斜（skew）等。

Transform 动画函数分为三个维度，即：一维动画、二维动画和三维动画。

- 一维动画函数：比如 translateX()、translateY()、scaleX()、scaleY()、skewX() 和 skewY() 等，通常，一维动画函数接收一个参数，表示在相关唯独方向的形变量。
- 二维动画函数：比如 translate()、scale()、rotate() 和 skew() 等，通常（除 rotate 动画外），二维动画函数接收两个参数，分别表示在 X、Y 轴方向上的形变量。
- 三维动画函数：比如 rotateX()、rotateY()、rotate3d()、translateZ()、translate3d()、scaleZ() 和 scale3d() 等。

### 1.2 Transform 动画属性

Transform 动画相关的属性有以下几个。

- transform
- transform-origin
- transform-style
- perspective
- perspective-origin
- backface-visibility
- transform-box

下面，分别对他们进行介绍。

#### （1）transform 属性

通过使用 transform 属性，可以实现元素的平移、缩放、旋转和倾斜等效果。transform 属性语法如下。

```css
transform: none | <transform-function>  [<transform-function>]*
```

默认值为 `none`，即表示元素不进行变形。`transform` 属性可以接受一系列的 `<transform-function>`，表示一个或多个变形函数，中间用空格分开。

下面列出了常用的 2D 变形函数及其功能。

- translate()：移动元素，可以根据俄 X 轴和 Y 轴坐标重新定位元素位置。在此基础上有两个扩展函数 translateX() 和 translateY()。
- scale()：缩小或放大函数，可以让元素尺寸发生变化。在此基础上有两个扩展函数 scaleX() 和 scaleY()。
- rotate()：旋转元素。
- skew()：让元素倾斜。在此基础上有两个扩展函数 skewX() 和 skewY()。
- matrix()：定义矩阵变形，基于 X 轴和 Y 轴坐标重新定位元素位置。

下面列出了常用的 3D 变形函数及其功能。

- translate3d()：移动元素，用来指定 3D 变形效果。
- translate()：此函数可以接受三个参数，通过指定三个参数，可在 3D 纬度实现平移效果。
- scale3d()：缩放元素。
- scaleZ()：用于指定 Z 轴的缩放量。
- rotateX()、rotateY()、rotateZ()：用于指定元素分别围绕 X、Y 和 Z 轴进行旋转。
- perspective()：用于指定一个透视投影矩阵。
- matrix3d()：定义矩阵变形。

#### （2）transform-origin 属性

`transform-origin` 属性用来指定元素的中心点位置。默认情况，变形的原点在元素的中心点，或者是元素 X 轴和 Y 轴的 50% 处。没有使用 `transform-origin` 改变元素原点位置的情况下，CSS 变形进行的旋转、移位、缩放等操作都是以元素自己中心（变形原点）位置进行变形的。但很多时候需要在不同的位置对元素进行变形操作，这时就可以使用 `transform-origin` 来对元素进行原点位置改变，使元素原点不在元素的中心位置。

`transform-origin` 属性值可以是百分比、em、px 等具体的值，也可以是 top、right、bottom、left 和 center 这样的关键词。比如，以 rotate() 为例，以下取值都是合法的。

```css
transform-origin: 0;
transform-origin: 0 50%;
transform-origin: top;
transform-origin: top center;
```

#### （3）transform-style 属性

`transform-style` 属性用于指定嵌套元素如何在 3D 空间中呈现。主要有两个属性值：`flat` 和 `preserve-3d`。

```css
transform-style: flat | preserve-3d;
```

- `flat`：默认值，表示所有子元素在 2D 平面呈现。
- `preserve-3d`：所有子元素在3D空间中呈现。

如果对一个元素设置了 `transform-style` 的值为 flat，该元素的所有元素都将被平展到该元素的 2D 平面中进行呈现。沿着 X 轴或 Y 轴方向旋转该元素将导致位于正或负 Z 轴位置的子元素显示在该元素的平面上，而不是它的前面或者后面。如果对一个元素设置了 `transform-style` 的值为 `preserve-3d`，它表示不执行平展操作，它的所有子元素位于 3D 空间中。

注意，如果元素设置了 `transform-style` 为 preserve-3d，就不能为了防止子元素出容器而设置 overflow: hidden，如果设置了 overflow:hidden 同样可以迫使子元素出现在同一平面（即和元素设置了 `transform-style` 为 flat 一样的效果）。

#### （4）perspective 属性

`perspective` 属性用于设置查看者的位置，并将可视内容映射到一个视锥上，继而投到一个 2D 视平面上。如果不指定透视，则 Z 轴空间中的所有点将平铺到同一个 2D 平面中，并且变换结果中将不存在景深概念。

上面的描述可能让人难以理解一些，其实对于perspective 属性，可以简单地理解为视距，用来设置用户和元素 3D 空间 Z 平面之间的距离。而其效应由他的值来决定，值越小，用户与 3D 空间 Z 平面距离越近，视觉效果更令人印象深刻；反之，值越大，用户与 3D 空间 Z 平面距离越远，视觉效果就很小。

`perspective` 属性语法如下。

```css
perspective: none | <length>;
```

- `none`：默认值，表示无限的角度来看 3D 物体，但看上去是平的。
- `<length>`：接受一个长度单位大于 0 的值，其单位不能为百分比值。值越大，角度出现的越远，从而创建一个相当低的强度和非常小的 3D 空间变化。反之，值越小，角度出现的越近，从而创建一个高强度的角度和一个大型 3D 变化。

在3D变形中，除了 perspective 属性可以激活一个 3D 空间之外,在3D变形的函数中的 perspective() 也可以激活3D空间。不同的地方是：perspective 用在舞台元素上（变形元素们的共同父元素）；perspective() 就是用在当前变形元素上，并且可以与其他的 transform 函数一起使用。

□ perspective 属性和 perspective()函数功能一样,但其取值以及运用的对象有所不同。
□ perspective 属性可以取值为none或长度值;而perspective()函数取值只能大于0,
如果取值为0或比0小的值,将无法激活3D空间;
□ perspective 属性用于变形对象父元素;而perspective()函数用于变形对象自身,并和
副分
transform 其他函数一起使用。

#### （5）perspective-origin 属性

perspective-origin 属性用来决定 perspective 属性的源点角度。它实际上设置了 X 轴和 Y 轴位置，在该位置观看者好像在观看该元素的子元素。

perspective-origin 属性的语法如下。

```css
perspective-origin: [<percentage> <length> | left | center | right | top | bottom] |
[[<percentage> <length> | left | center | right] && [<percentage> |
<length> | top | center | bottom]]
```

该属性默认值为“50%50%”(也就是center),可以设置为一个值,也可以设置为两个长度值。第一个长度值指定相对于元素的包含框的X轴上的位置。它可以是长度值(以受支持的长度单位表示)、百分比或以下三个关键词之一。

- left：在包含框的X轴方向长度的0%。
- center：中间点。
- right：长度的100%。

第二个长度值指定相对于元素的包含框的Y轴上的位置。它可以是长度值、百分比或以下三个关键词之一。

- top: 在包含框的Y轴方向长度的0%。
- center:中间点。
- bottom:长度的100%。
注意,为了指转换子元素变形的深度，perspective-origin 属性必须定义父元素上。通常 perspective-origin属性本
身不做任何事情,它必须被定义在设置 perspective 属性的元素上。换句话说,属性需要与 perspective-origin perspective 属性结合起来使用,以便将视点移至元素的中心以外位置。

#### （6） 属性

backface-visibility 属性决定元素旋转背面是否可见。对于未旋转的元素,该元素的正面面向观看者。当其Y轴旋转约 180 度时会导致元素的背面面对观众。

backface-visibility 属性语法如下。

```css
backface-visibility: visible | hidden
```

该属性设置为以下两个关键词之一

- visible:默认值,反面可见。
- hidden:反面不可见。

一个元素的可见性与 backface-visibility:hidden 决定如下。

- 元素在3D环境下渲染上下文,将根据3D变形矩阵来计算,反之元素不在3D环境下渲染上下文,将根据2D变形矩阵来计算。
- 如果组件的矩阵在第 3 行、3 列是负值,元素反面是隐藏,反之是可见的。

简单来说，backface-visibility 属性可用于隐藏内容的背面。默认情况下，背面可见，这意味着即使在翻转后，旋转的内容仍然可见。当 backface-visibility 设置为 hidden 时，旋转后内容将隐藏，因为旋转后正面将不再可见。

#### （7） 属性

## 二、Transition 动画

[CSS Transitions](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_transitions)

## 三、Animation 动画


