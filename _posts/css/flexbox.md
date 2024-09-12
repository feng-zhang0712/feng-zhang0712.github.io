# Flex 布局

一、简介
Flex（Flexible Box 的缩写）：指弹性布局。
Flex 布局是一维布局模型，用来给子元素提供空间分布和对齐能力。
/* 将容器指定为 Flex 布局 */
display: flex;

/* 行内元素也可以使用 Flex 布局 */
display: inline-flex;

/* Webkit 内核的浏览器，必须加上 -webkit 前缀 */
display: flex;
display: -webkit-flex; /* Safari */

注意，设为 Flex 布局以后，子元素的 float、clear 和 vertical-align 属性将失效。
二、基本概念
Flex 容器：指采用 Flex 布局的元素。
Flex 项目：指 Flex 容器的所有子元素。
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。
主轴的开始位置叫做 main start，结束位置叫做 main end；
交叉轴的开始位置叫做 cross start，结束位置叫做 cross end。
项目默认沿主轴排列。单个项目占据的主轴空间叫做 main size，占据的交叉轴空间叫做 cross size。
三、容器的属性
以下6个属性设置在容器上。
flex-direction
justify-content
align-items
flex-wrap
flex-flow
align-content
1. flex-direction
决定主轴的方向（即项目的排列方向）。
flex-direction: row（默认）|row-reverse|column|column-reverse;


2. justify-content
定义项目在主轴上的对齐方式。
justify-content: flex-start|flex-end|center|space-between|space-around ...;


3. align-items
定义项目在交叉轴上的对齐方式。
align-items: flex-start|flex-end|center|baseline|stretch ...;


2. flex-wrap
指定项目如果一行放不下，如何换行。
flex-wrap: nowrap（默认|wrap|wrap-reverse;


3. flex-flow
flex-direction 和 flex-wrap 的简写，默认值为 row nowrap。
flex-flow：<flex-direction>;
flex-flow：<flex-wrap>;
flex-flow：<flex-direction> <flex-wrap>;


6. align-content
定义多行的项目在交叉轴上的对齐方式。
如果项目只有一根轴线，该属性不起作用（即 flex-wrap: nowrap）。
align-content: flex-start|flex-end|center|space-between|space-around|stretch;

四、项目的属性
以下6个属性设置在项目上。
order
flex-grow
flex-shrink
flex-basis
flex
align-self
1. order
定义项目的排列顺序。数值越小，排列越靠前，默认为0。
拥有相同 order 属性值的元素按照它们在源代码中出现的顺序进行布局。
注意：order 仅仅对元素的视觉顺序产生作用，并不会影响元素的逻辑或 tab 顺序。
order: <integer>;


2. flex-grow
定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
如果所有项目的 flex-grow 都为1，则它们将等分剩余空间。
如果一个项目的 flex-grow 为 2，其他项目都为 1，则前者占据的剩余空间将比其他项多一倍。
flex-grow: <number [0,∞]>; /* default 0 */


3. flex-shrink
定义项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
如果所有项目的 flex-shrink 都为1，当空间不足时，都将等比例缩小。
如果一个项目的 flex-shrink 为 0，其他项目都为 1，则空间不足时，前者不缩小。
负值对该属性无效。
flex-shrink: <number [0,∞]>; /* default 1 */


4. flex-basis
定义在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。默认值为 auto，即项目的本来大小。
它可以设为跟 width 或 height 属性一样的值（比如350px），则项目将占据固定空间。
flex-basis: <length> | auto; /* default auto */


5. flex
flex-grow, flex-shrink 和 flex-basis 的简写，默认值为 0 1 auto。后两个属性可选。
该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
建议优先使用这个属性，而不是单独写三个分离的属性，因为浏览器会推算相关值。
flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ];


6. align-self
允许单个项目有与其他项目不一样的对齐方式，可覆盖 align-items 属性。默认值为 auto，表示继承父元素的 align-items 属性，如果没有父元素，则等同于 stretch。
该属性的取值除了 auto，其他都与 align-items 完全一致。
align-self: auto（默认）|flex-start|flex-end|center|baseline|stretch;


参考：
弹性盒子
弹性盒布局
CSS 弹性盒子布局
Flex 布局教程：语法篇
Flex 布局教程：实例篇
