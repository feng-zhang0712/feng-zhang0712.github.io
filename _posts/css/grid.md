# Grid 网格布局

网格布局将网页划分成一个个网格，通过任意组合不同的网格，做出各种各样的布局。
Flex 布局是轴线布局，只能指定"项目"针对轴线的位置，是一维布局。
Grid 布局将容器划分成"行"和"列"，产生单元格，并指定"项目所在"的单元格，是二维布局。
一、基本概念
容器：指采用网格布局的区域。
项目：指容器内采用网格定位的子元素。项目只能是容器的顶层子元素，不包含项目的子元素。Grid 布局只对项目生效。
行（row）：指容器中的水平区域。
列（column）：指容器中的垂直区域。
单元格（cell）：指行和列的交叉区域。正常情况下，n 行和 m 列会产生 n x m 个单元格。
网格线（grid line）：指划分网格的线。水平网格线划分出行，垂直网格线划分出列。
二、容器属性
Grid 布局的属性分成两类。一类定义在容器上，称为容器属性；另一类定义在项目上，称为项目属性。

1. display
display: grid 指定一个容器采用网格布局。
默认情况下，容器元素都是块级元素，但也可以设成行内元素。
display: grid || inline-grid;

注意，设置为网格布局后，容器中项目的 float、display: inline-block、vertical-align 和 column-* 等设置将失效。

2. grid-template-columns、grid-template-rows
grid-template-rows：定义每行的行高；
grid-template-columns：定义每列的列宽。
下面代码指定了一个三行三列的网格，行高和列宽都是100px。
.container {
  display: grid;
  grid-template-rows: 100px 100px 100px;
  grid-template-columns: 100px 100px 100px;
}

.container {
  display: grid;
  grid-template-rows: 33.33% 33.33% 33.33%;
  grid-template-columns: 33.33% 33.33% 33.33%;
}
/* 上面的例子，没有使用绝对单位，而是百分比 */


repeat()
使用 repeat(<重复次数>, <重复值>) 函数，可以简化重复的值。
.container {
  display: grid;
  grid-template-rows: repeat(3, 100px);
  grid-template-columns: repeat(3, 100px);
}


repeat() 也可以重复某种模式。
grid-template-columns: repeat(2, 100px 20px 80px);


auto-fill 关键字
auto-fill 关键字表示自动填充。
有时，单元格大小是固定的，但容器大小不确定。auto-fill 关键表示每行（列）容纳尽可能多的单元格。
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, 100px);
}

除了auto-fill，还有一个关键字 auto-fit，两者行为基本相同。只有当容器足够宽，可以在一行容纳所有单元格，并且单元格宽度不固定的时候，才会有行为差异：
auto-fill 会用空格子填满剩余宽度；
auto-fit 会尽量扩大单元格的宽度。

fr 关键字
fr 关键字（fraction 的缩写，意为"片段"）用来表示比例关系。
如果两列的宽度分别为 1fr 和 2fr，就表示后者是前者的两倍。
.container {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
/* 两个相同宽度的列 */


fr 可以与绝对长度单位结合使用。
.container {
  display: grid;
  grid-template-columns: 150px 1fr 2fr;
}
/* 第一列的宽度为150px，第二列的宽度是第三列的一半 */


minmax()
minmax(<最小值>,<最大值>) 函数产生一个长度范围，表示长度就在这个范围中。
grid-template-columns: 1fr 1fr minmax(100px, 1fr);
/* minmax(100px, 1fr) 表示列宽不小于 100px，不大于 1fr */


auto 关键字
auto 关键字表示由浏览器自己决定长度。
grid-template-columns: 100px auto 100px;

上面代码中，第2列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了min-width，且这个值大于最大宽度。

网格线的名称
grid-template-columns 和 grid-template-rows 可以使用方括号，指定每根网格线的名字，方便以后的引用。
.container {
  display: grid;
  grid-template-columns: [c1] 100px [c2] 100px [c3] auto [c4];
  grid-template-rows: [r1] 100px [r2] 100px [r3] auto [r4];
}

上面代码指定网格布局为3行 x 3列，因此有4根垂直网格线和4根水平网格线。方括号里面依次是这八根线的名字。
注意，网格布局允许同一根线有多个名字，比如 [fifth-line row-5]。

布局实例
grid-template-columns 属性对于网页布局非常有用。两栏式布局只需要一行代码。
.wrapper {
  display: grid;
  grid-template-columns: 70% 30%;
}
/* 上面的代码，将左边栏设为70%，右边栏设为30% */

grid-template-columns: repeat(12, 1fr);
/* 传统的十二网格布局，写起来也很容易 */


row-gap、column-gap、grid-gap
row-gap 属性设置行间距，column-gap 属性设置列间距。
.container {
  row-gap: 20px;
  column-gap: 20px;
}


grid-gap 属性是 column-gap 和 row-gap 的合并简写形式，语法如下。
grid-gap: <row-gap> <column-gap>;


grid-template-areas
grid-template-areas 属性用于定义区域。一个区域由单个或多个单元格组成。



.container {
  display: grid;
  grid-template-columns: 100px 100px 100px;
  grid-template-rows: 100px 100px 100px;
  grid-template-areas: 'a b c'
                       'd e f'
                       'g h i';
}
/* 上面代码先划分出9个单元格，然后将其定名为 a 到 i 的九个区域，分别对应这九个单元格 */


多个单元格合并成一个区域的写法如下。
grid-template-areas: 'a a a'
                     'b b b'
                     'c c d';
/* 上面的代码，将9个单元格分成a、b、c 和 d 四个区域 */


如果某些区域不需要利用，则使用"点"（.）表示。
grid-template-areas: 'a . c'
                     'd . f'
                     'g . i';
/* 上面代码中，中间一列为点，表示没有用到该单元格，或者该单元格不属于任何区域 */

注意，区域的命名会影响到网格线。
每个区域的起始网格线，会自动命名为 区域名-start，终止网格线自动命名为 区域名-end。
比如，区域名为 header，则起始位置的水平网格线和垂直网格线叫做 header-start，终止位置的水平网格线和垂直网格线叫做 header-end。

grid-auto-flow
grid-auto-flow 属性，决定容器内子元素的排列顺序，默认值是 row，即"先行后列"，先填满第一行，再开始放入第二行。也可以将它设成 column，变成"先列后行"。
grid-auto-flow: row（默认）| column | row dense | column dense;

row dense 和 column dense 这两个值主要用于，某些项目指定位置以后，剩下的项目怎么自动放置。

justify-items、align-items、place-items
justify-items：设置单元格内容的水平位置。
align-items：设置单元格内容的垂直位置。
place-items：表示 align-items 和 justify-items 的合并简写形式。
.container {
  justify-items (align-items): start | end | center | stretch;
}

place-items: <align-items> <justify-items>;


justify-content、align-content、place-content
justify-content：指整个内容区域在容器里的水平位置。
align-content：指整个内容区域的垂直位置。
place-content：表示 align-content 和 justify-content 的合并简写形式。
.container {
  justify-content (align-content): start | end | center | stretch | space-around | space-between | space-evenly;
}

place-content: <align-content> <justify-content>;


grid-auto-columns、grid-auto-rows
有时候，一些项目的指定位置，在现有网格的外部。这时，浏览器会自动生成多余的网格，以便放置项目。
grid-auto-columns 和 grid-auto-rows 用来设置浏览器自动创建的多余网格的列宽和行高。它们的写法与 grid-template-columns 和 grid-template-rows 完全相同。如果不指定这两个属性，浏览器完全根据单元格内容的大小，决定新增网格的列宽和行高。

grid-template、grid
grid-template 属性是以下三个属性的合并简写形式：
grid-template-columns
grid-template-rows
grid-template-areas 
grid 属性是以下六个属性的合并简写形式：
grid-template-rows
grid-template-columns
grid-template-areas
grid-auto-rows
grid-auto-columns
grid-auto-flow
从易读易写的角度考虑，建议不要合并属性。

三、项目属性
项目的位置是可以指定的，具体方法就是指定项目的四个边框，分别定位在哪根网格线。
grid-column-start：左边框所在的垂直网格线；
grid-column-end：右边框所在的垂直网格线；
grid-row-start：上边框所在的水平网格线；
grid-row-end：下边框所在的水平网格线
.item-1 {
  grid-column-start: 2;
  grid-column-end: 4;
}
/* 上面代码指定，1号项目的左边框是第2根垂直网格线，右边框是第4根垂直网格线 */

.item-1 {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 2;
  grid-row-end: 4;
}
/* 上面的例子指定四个边框位置的效果  */


这四个属性的值，除了指定为第几个网格线，还可以指定为网格线的名字。
.item-1 {
  grid-column-start: header-start;
  grid-column-end: header-end;
}
/* 上面代码中，左边框和右边框的位置，都指定为网格线的名字 */


这四个属性的值还可以使用 span 关键字，表示"跨越"，即左右（上下）边框之间跨越多少个网格。
.item-1 {
  grid-column-start: span 2;
}
/* 上面代码表示，1号项目的左边框距离右边框跨越2个网格 */


使用这四个属性，如果产生了项目的重叠，则使用 z-index 属性指定项目的重叠顺序。

grid-column、grid-row
grid-column：表示 grid-column-start 和 grid-column-end 的合并简写形式。
grid-row：表示 grid-row-start 和 grid-row-end 的合并简写形式。
.item {
  grid-column (grid-row): <start-line> / <end-line>;
}

.item-1 {
  grid-column: 1 / 3;
  grid-row: 1 / 2;
}
/* 等同于 */
.item-1 {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 2;
}
/* 上面代码中，项目 item-1 占据第1行，从第1根列线到第3根列线 */


这两个属性，可以使用 span 关键字，表示跨越多少个网格。
.item-1 {
  background: #b03532;
  grid-column: 1 / 3;
  grid-row: 1 / 3;
}
/* 等同于 */
.item-1 {
  background: #b03532;
  grid-column: 1 / span 2;
  grid-row: 1 / span 2;
}
/* 上面代码中，项目 item-1 占据的区域，包括第1行 + 第2行、第1列 + 第2列 */


斜杠以及后面的部分可以省略，默认跨越一个网格。
.item-1 {
  grid-column: 1;
  grid-row: 1;
}
/* 上面代码中，项目 item-1 占据左上角第1个网格 */


grid-area
grid-area 属性指定项目放在哪个区域。
.item-1 { grid-area: e; }


grid-area 属性还可用作 grid-row-start、grid-column-start、grid-row-end 和 grid-column-end 的合并简写形式，直接指定项目的位置。
.item {
  grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
}


justify-self、align-self、place-self
justify-self：设置单元格内容的水平位置，跟 justify-items 的用法完全一致，只作用于单个项目。
align-self：设置单元格内容的垂直位置，跟 align-items 的用法完全一致，只作用于单个项目。
.item {
  justify-self (align-self): start | end | center | stretch;
}


place-self：表示 align-self 和 justify-self 的合并简写形式。
place-self: <align-self> <justify-self>;


参考：
CSS Grid 网格布局教程
网格
网格布局
