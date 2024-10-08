# 树

树结构是一类重要的非线性数据结构。直观来看，树是以分支关系定义的层次结构。树结构在客观世界中广泛存在，如人类社会的族谱和各种社会组织机构都可用树来形象表示。树在计算机领域中也得到广泛应用，尤以二叉树最为常用。如在操作系统中，用树来表示文件目录的组织结构；在编译系统中，用树来表示源程序的语法结构；在数据库系统中，树结构也是信息的重要组织形式之一。

## 一、树的定义

树（Tree）是 n（n=0）个结点的有限集，它或为空树（n=0）；或为非空树，对于非空树 T：

- 有且仅有一个称之为根的结点；
- 除根结点以外的其余结点可分为 m（m>0）个互不相交的有限集 $T_1$, $T_2$, ..., $T_m$， 其中每一个集合本身又是一棵树，并且称为根的子树（SubTree）。

例如，在下图中，（a）是只有一个根结点的树；（b）是有13个结点的树，其中 A 是根，其余结点分成3个互不相交的子集：$T_1$={B, E, F, K, L}，$T_2$={C, G}，$T_3$ = {D, H, I, J, M}。$T_1$、$T_2$ 和 $T_3$ 都是根 A 的子树，且本身也是一棵树。例如 $T_1$，其根为 B，其余结点分为两个互不相交的子集：$T_{11}$={E, K, L}，$T_{12}$={F}。$T_{11}$ 和 $T_{12}$ 都是 B 的子树。而 $T_{11}$ 中 E 是根，{K} 和 {L} 是 E 的两棵互不相交的子树，其本身又是只有一个根结点的树。

![树的示例](/assets/tree.png)

树的结构定义是一个递归的定义，即在树的定义中又用到树的定义，它道出了树的固有特性。树还可有其他的表示形式，如图5.2所示为图5.1(b)中树的各种表示。其中(a)是以嵌套集合(即是一些集合的集体，对于其中任何两个集合，或者不相交，或者一个包含另一个)的形式表示的；(b)是以广义表的形式表示的，根作为由子树森林组成的表的名字写在表的左边；(c)用的是凹入表示法(类似书的编目)。表示方法的多样化，正说明了树结构在日常生活中及计算机程序设计中的重要性。一般来说，分等级的分类方案都可用层次结构来表示，也就是说，都可由一个树结构来表示。

![树的其他3种表示法](/assets/tree-in-defination.png)

下面介绍树结构中的一些基本术语。

## 二、树的基本术语

- **结点**：树中的一个独立单元，包含一个数据元素及若干指向其子树的分支。如[树的定义部分示例图](#一树的定义)中的 A、B、C、D 等。（下面术语中均以图为例来说明）
- **结点的度**：结点拥有的子树数称为结点的度。例如，A的度为 3，C 的度为 1, F的度为 0。
- **树的度**：树的度是树内各结点度的最大值。示例图所示的树的度为3。
- **叶子**：度为0的结点称为叶子或终端结点。结点 K、L、F、G、M、I、J 都是树的叶子。
- **非终端结点**：度不为0的结点称为非终端结点或分支结点。除根结点之外，非终端结点也称为内部结点。
- **双亲和孩子**：结点的子树的根称为该结点的孩子，相应地，该结点称为孩子的双亲。例如，B的双亲为A，B的孩子有E和F。
- **兄弟**：同一个双亲的孩子之间互称兄弟。例如，H、I和J互为兄弟。
- **祖先**：从根到该结点所经分支上的所有结点。例如，M的祖先为A、D和H。
- **子孙**：以某结点为根的子树中的任一结点都称为该结点的子孙。如B的子孙为E、K、L和F。
- **层次**：结点的层次从根开始定义起，根为第一层，根的孩子为第二层。树中任一结点的层次等于其双亲结点的层次加1。
- **堂兄弟**：双亲在同一层的结点互为堂兄弟。例如，结点 G与E、F、H、I、J互为堂兄弟。
- **树的深度**：树中结点的最大层次称为树的深度或高度。示例图所示的树的深度为4。
- **有序树和无序树**：如果将树中结点的各子树看成从左至右是有次序的（即不能互换），则称该树为有序树，否则称为无序树。在有序树中最左边的子树的根称为第一个孩子，最右边的称为最后一个孩子。
- **森林**：是 m（m≥0）棵互不相交的树的集合。对树中每个结点而言，其子树的集合即为森林。由此，也可以用森林和树相互递归的定义来描述树。

就逻辑结构而言，任何一棵树都是一个二元组 Tree=(root, F)。其中 root 是数据元素，称作树的根结点；F 是 m(m≥0)棵树的森林，F=($T_1$, $T_2$, ..., $T_n$)，其中 $T_i$=($r_i$, $F_i$) 称作根 root 的第 i 棵子树；当 m≠0 时，在树根和其子树森林之间存在下列关系：

$$RF = {<root, r_i>|i=1, 2, ..., m, m>0}$$

这个定义将有助于得到森林和树与二叉树之间转换的递归定义。

## 三、案例引入

随着大数据时代的到来，如何采用有效的数据压缩技术，来节省数据文件的存储空间和网络传输时间越来越引起人们的重视。

### 案例：数据压缩问题

在数据通信、数据压缩问题中，需要将数据文件转换成二进制字符 0、1 组成的二进制串，称之为编码。

假设待压缩的数据为 `abcdabcdaaaaabbbdd`，文件中只包含 a、b、c、d 四种字符，如果采用等长编码，每个字符编码取两位即可，表（a）所示为一种等长编码方案。上述数据为 18 个字符，其编码总长度为 36 位。但这并非最优的编码方案，因为每个字符出现的频率不同，如果在编码时考虑字符出现的频率，使频率高的字符采用尽可能短的编码，频率低的字符采用稍长的编码，来构造一种不等长编码，则会获得更好的空间效率，这也是文件压缩技术的核心思想。如表（b）所示为一种不等长编码方案，采用这种编码方案时，其编码总长度为 35 位。但对于不等长编码，如果设计得不合理，便会给解码带来困难。例如，对于如表（c）所示的另一种不等长编码方案，上述数据编码后为 `00101111100101111100000010101111111`。但是，这样的编码数据无法翻译，例如，传送过去的字符串中前 4 个字符的子串 `0010` 就可有不同的译法，或是 `aba`，或是 `ac`。因此，若要设计长短不等的编码，必须满足一个条件：**任何一个字符的编码都不是另一个字符的编码的前缀**。表（b）所示的编码方案便满足这个条件。

那么如何设计有效的用于数据压缩的二进制编码呢？我们可以利用一种特殊的树结构：**哈夫曼树** 来设计。

表（b）所示的编码是以字符 a、b、c、d 在数据串 `abcdabcdaaaaabbbdd` 中出现的次数 7、5、2、4 为权值，构造如下图所示的哈夫曼树，其 4 个叶子结点分别表示 a、b、c、d 这 4 个字符，且约定左分支标记为 0，右分支标记为 1，则根结点到每个叶子结点路径上的 0、1 序列即为相应字符的编码。由图 5.4 所得 a、b、c、d 的二进制编码分别为 0、10、110 和 111。

![3种不同的编码方案](/assets/tree-encode-in-data-compression.png)

![哈夫曼树及编码示例](/assets/haffman-tree-example.png)*哈夫曼树及编码示例*

### 四、树和二叉树的抽象数据类型定义

根据树的结构定义,加上树的一组基本操作就构成了树的抽象数据类型定义:
ADT Tree{
数据对象D:D是具有相同特性的数据元素的集合。
数据关系 R:若D为空集,则称为空树;
若D仅含一个数据元素,则为空集,否则R={H},H是如下二元关系:
(1)在D中存在唯一的称为根的数据元素 root,它在关系H下无前驱;
(2)若D-{root}≠,则存在D-{root}的一个划分D,D2,..., Dm(m>0),对任意j≠k(1≤j, k≤m)有
DjnDx = 4,且对任意的i(1≤i≤m),唯一存在数据元素x;∈D,有<root,xi>∈H;
(3)对应于D-{root}的划分,H-{<root,xi>,…,<root,x> =有唯一的一个划分 H1,H2,
Hm(m>0),对任意j≠k(1≤j, k≤m)有HgnHk=0,且对任意i(1≤i≤m),H是D上的二元关
系,(Di, {H})是一棵符合本定义的树,称为根 root 的子树。
seT TOA
基本操作 P:
InitTree(&T)
操作结果:构造空树T。
DestroyTree(&T)
初始条件:树存在。
操作结果:销毁树 T。
CreateTree (&T, definition) 01
初始条件:definition 给出树的定义。
操作结果:按definition 构造树 T。
ClearTree (&T)
初始条件:树存在。
操作结果:将树清为空树。
eerTyssaia POA

TreeEmpty (T)
初始条件:树存在。
操作结果:若为空,则返回true,否则false。
TreeDepth (T)
初始条件:树存在。
操作结果:返回T的深度。
Root (T)
初始条件:树存在。
操作结果:返回T的根。
Value (T, cur_e)
初始条件:树存在,cur_e
ar_e是T中某个结点。
操作结果:返回cure的值。
Assign (T, cur_e,value)
初始条件:树存在,cure是T中某个结点。
操作结果:结点cure赋值为value。
Parent (T, cur_e);
初始条件:树T存在,cur_e是中某个结点。
操作结果:若 cur_e是T的非根结点,则返回它的双亲,否则函数值为“空”。
LeftChild(T,cur_e)
初始条件:树T存在,cur_e是中某个结点。
操作结果:若cure 是T的非叶子结点,则返回它的最左孩子,否则返回“空”。
RightSibling (T,cur_e)
初始条件:树T存在,cur_e是中某个结点。
操作结果:若 cur_e有右兄弟,则返回它的右兄弟,否则函数值为“空”。
InsertChild(&T,p,i,c)
8.2
初始条件:树存在,p指向T中某个结点,1≤i≤p所指结点的度+1,非空树c与不相交。
操作结果:插入c为T中p指结点的第i棵子树。
DeleteChild (&T, p,i)
初始条件:树T存在,p指向T中某个结点,1≤i≤p 指结点的度。
操作结果:删除T中所指结点的第i棵子树。
TraverseTree (T)
初始条件:树存在。
操作结果:按某种次序对T的每个结点访问一次。
} ADT Tree

## 五、树和森林

本节将讨论树的表示及其遍历操作，并建立森林与二叉树的对应关系。

5.6.1 树的存储结构
第5章树和二叉树
在大量的应用中,人们曾使用多种形式的存储结构来表示树。这里介绍3种常用的表示
方法。
1. 双亲表示法
这种表示方法中,以一组连续的存储单元存储树的结点,每个结点除了数据域data外,还附
设一个 parent 域用以指示其双亲结点的位置,其结点形式如图5.18所示。
例如,图5.19 所示为一棵树及其双亲表示的存储结构。
数组下标

图5.18 双亲表示法的结点形式
图5.19 树的双亲表示法示例
这种存储结构利用了每个结点(除根以外)只有唯一的双亲的性质。在这种存储结构下,求
结点的双亲十分方便,也很容易求树的根,但求结点的孩子时需要遍历整个结构。
2.孩子表示法
由于树中每个结点可能有多棵子树,则可用多重链表,即每个结点有多个指针域,其中每个
指针指向一棵子树的根结点,此时链表中的结点可以有如图5.20所示的两种结点格式。
data
child1 child2
childd
data
degree child1
child2
...
childd blirfotard A
图5.20 孩子表示法的两种结点
gnildierxan
若采用第一种结点格式,则多重链表中的结点是同构的,其中d为树的度。由于树中很多结
点的度小于d,所以链表中有很多空链域,空间较浪费,不难推出,在一棵有n个结点度为k的
树中必有n(k-1)+1个空链域。
若采用第二种结点格式,则多重链表中的结点是不同构的,其中d为结点的度,degree 域的
值同d。此时,虽能节约存储空间,但操作不方便。
另一种办法是,把每个结点的孩子结点排列起来,看成是一个线性表,且以单链表做存储结
构,则n个结点有n个孩子链表(叶子的孩子链表为空表)。而n个头指针又组成一个线性表,为
了便于查找,可采用顺序存储结构。
图 5.21(a)所示为图5.19中的树的孩子表示法。与双亲表示法相反,孩子表示法便于
那些涉及孩子的操作的实现。可以把双亲表示法和孩子表示法结合起来,即将双亲表示和孩
子链表合在一起。图5.21(b)所示的就是这种存储结构的一例,它和图5.21(a)表示的是
同一棵树。

(a)孩子链表
(b)带双亲的孩子链表
图5.21 图5.19的树的另外两种表示法
3.孩子兄弟法
又称二叉树表示法,或二叉链表表示法,即以二叉链表做树的存储结构。链表中结点的两个
链域分别指向该结点的第一个孩子结点和下一个兄弟结点,分别命名为firstchild 域和 nextsibling
域,其结点形式如图 5.22 所示。
firstchild
data
nextsibling
图5.22 孩子兄弟表示法的结点
steb
//---- -树的二叉链表(孩子-兄弟)存储表示-
typedef struct CSNode{
ElemType data;
struct CSNode *firstchild, *nextsibling;
}CSNode, *CSTree;
图5.23 所示为图5.19中的树的孩子兄弟链表。利用这种
存储结构便于实现各种树的操作。首先易于实现找结点孩子
等的操作。例如,若要访问结点x的第i个孩子,则只要先
从 firstchild 域找到第1个孩子结点,然后沿着孩子结点的
nextsibling 域连续走-1步,便可找到x的第i个孩子。当然,
如果为每个结点增设一个 parent 域,则同样能方便地实现查
找双亲的操作。
这种存储结构的优点是它和二叉树的二叉链表表示完
全一样,便于将一般的树结构转换为二叉树进行处理,利
用二叉树的算法来实现对树的操作。因此孩子兄弟表示法
是应用较为普遍的一种树的存储表示方法。
5.6.2 森林与二叉树的转换水
RA
AD
AB
MEN
F
AG
图5.23 图5.19中树的二叉链表表示法
平和)赤益干期:
从树的二叉链表表示的定义可知,任何一棵树对应的二叉树,其根结点的右子树必空。若
把森林中第二棵树的根结点看成是第一棵树的根结点的兄弟,则同样可导出森林和二叉树的对应
关系。
例如,图5.24 所示为森林与二叉树之间的对应关系。

Page 143
G 森林与二叉树对应A
第5章树和二叉树
(B
B
E
树与二叉树对应
E
树根相连
①
①
图5.24 森林与二叉树的对应关系示例
这个一一对应的关系说明森林或树与二叉树可以相互转换。

1. 森林转换成二叉树

如果F = {Ti, T2,…,Tm}是森林,则可按如下规则转换成一棵二叉树B=(root, LB, RB)。
(1)若F为空,即m=0,则B为空树;
(2)若F非空,即m≠0,则B的根 root即为森林中第一棵树的根ROOT(Ti);B的左子树
LB 是从中根结点的子树森林F={T1,Ti2,...,Tim}转换而成的二叉树;其右子树RB是从森林
F' = {T2, T3,…,T}转换而成的二叉树。

2. 二叉树转换成森林

如果B = (root, LB,RB)是一棵二叉树,则可按如下规则转换成森林F= {T\, Tz, ..., Tm}:
(1)若B为空,则F为空;
(2)若B非空,则F中第一棵树的根ROOT(T)即为二叉树B的根 root;T中根结点的子
树森林F是由B的左子树LB转换而成的森林;F中除之外其余树组成的森林F={T2,T3,..., Tm}
是由B的右子树RB转换而成的森林。
从上述递归定义容易写出相互转换的递归算法。同时,森林和树的操作亦可转换成二叉树的
操作来实现。

5.6.3 树和森林的遍历

1. 树的遍历
由树结构的定义可引出两种次序遍历树的方法:一种是先根(次序)遍历树,即:先访问树
的根结点,然后依次先根遍历根的每棵子树;另一种是后根(次序)遍历,即先依次后根遍历每
棵子树,然后访问根结点。
例如,对图 5.19 所示的树进行先根遍历,可得树的先根序列为:
RADEBCF GHK
若对此树进行后根遍历,则得树的后根序列为:
扫
DEABGHKFCR
按照森林和树相互递归的定义,可以推出森林的两种遍历方法:先序遍历和中序遍历。
2. 森林的遍历
(1)先序遍历森林
若森林非空,则可按下述规则遍历:
① 访问森林中第一棵树的根结点;

数据结构(C语言版)(第2版)
② 先序遍历第一棵树的根结点的子树森林;
③ 先序遍历除去第一棵树之后剩余的树构成的森林。
(2)中序遍历森林
若森林非空,则可按下述规则遍历:
中序遍历森林中第一棵树的根结点的子树森林;
② 访问第一棵树的根结点;
③ 中序遍历除去第一棵树之后剩余的树构成的森林。
若对图 5.24 中所示的森林进行先序遍历和中序遍历,则分别得到森林的先序序列为:
中序序列为:
ABCDEFGHIJ
BCDAFEHJIG

由5.6.2小节森林与二叉树之间转换的规则可知,当森林转换成二叉树时,其第一棵树的子树
森林转换成左子树,剩余树的森林转换成右子树,则上述森林的先序和中序遍历即为其对应的二
叉树的先序和中序遍历。若对图5.24中所示的和森林对应的二叉树分别进行先序和中序遍历,可
得和上述相同的序列。
由此可见,当以二叉链表做树的存储结构时,树的先根遍历和后根遍历可借用二叉树的先序
遍历和中序遍历的算法实现。

## 六、哈夫曼树及其应用

树结构是一种应用非常广泛的结构,在一些特定的应用中,树具有一些特殊特点,利用这些
特点可以解决很多工程问题。在5.2节提出的应用案例5.1可以借助一种应用很广的树——哈夫曼
树来解决,本节便以哈夫曼树为例,说明二叉树的一个具体应用。
5.7.1 哈夫曼树的基本概念
哈夫曼(Huffman)树又称最优树,是一类带权路径长度最短的树,在实际中有广泛的用途。
哈夫曼树的定义,涉及路径、路径长度、权等概念,下面先给出这些概念的定义,然后再介绍哈
夫曼树。
(1)路径:从树中一个结点到另一个结点之间的分支构成这两个结点之间的路径。
(2)路径长度:路径上的分支数目称作路径长度。
(3)树的路径长度:从树根到每一结点的路径长度之和。
(4)权:赋予某个实体的一个量,是对实体的某个或某些属性的数值化描述。在数据结构中,
实体有结点(元素)和边(关系)两大类,所以对应有结点权和边权。结点权或边权具体代表什
么意义,由具体情况决定。如果在一棵树中的结点上带有权值,则对应的就有带权树等概念。
(5)结点的带权路径长度:从该结点到树根之间的路径长度与结点上权的乘积。
歐
(6)树的带权路径长度:树中所有叶子结点的带权路径长度之和,通常记作WPL = Z walk o
k=1
(7)哈夫曼树:假设有m个权值{W1,W2,..., Wm},可以构造一棵含n个叶子结点的二叉树,
每个叶子结点的权为w;,则其中带权路径长度 WPL 最小的二叉树称做最优二叉树或哈夫曼树。

Page 145
第5章树和二叉树
例如,图 5.25 中所示的3棵二叉树,都含4个叶子结点a、b、c、d,分别带权7、5、2、4,
它们的带权路径长度分别为
7
4
(a)WPL=7×2+5×2+2×2+4×2=36
5
(b)WPL=7×3+5×3+2×1+4×2= 46
(c)WPL=7×1+5×2+2×3+4×3= 35
图5.25 具有不同带权路径长度的二叉树
其中以(c)树的为最小。可以验证,它恰为哈夫曼树,即其带权路径长度在所有带权为7、5、2、
4的4个叶子结点的二叉树中居最小。
哈夫曼树中具有不同权值的叶子结点的分布有什么特点呢?从上面的例子中,可以直观地发
现,在哈夫曼树中,权值越大的结点离根结点越近。根据这个特点,哈夫曼最早给出了一个构造
哈夫曼树的方法,称哈夫曼算法。
5.7.2 哈夫曼树的构造算法
1.哈夫曼树的构造过程
(1)根据给定的n个权值{W1,W2,…,wn},构造n棵只有根结点的二叉树,这n棵二叉树构成
一个森林F。
(2)在森林 F 中选取两棵根结点的权值最小的树作为左右子树构造一棵新的二叉树,且
置新的二叉树的根结点的权值为其左、右子树上根结点的权
值之和。
7 5
4
7
5 6
a
(3)在森林F中删除这两棵树,同时将新得到的二叉树加
入F中。
(b)
7
11
18
(4)重复(2)和(3),直到F只含一棵树为止。这棵树
便是哈夫曼树。
a
在构造哈夫曼树时,首先选择权小的,这样保证权大的离
根较近,这样一来,在计算树的带权路径长度时,自然会得到
最小带权路径长度,这种生成算法是一种典型的贪心法。
过程。其中,根结点上标注的数字是所赋的权。
例如,图 5.26 所示为图5.25(c)所示的哈夫曼树的构造
(c)
(d)
图5.26 哈夫曼树的构造过程
137

Page 146
数据结构(C语言版)(第2版)
2.哈夫曼算法的实现
哈夫曼树是一种二叉树,当然可以采用前面介绍过的通用存储方法,而由于哈夫曼树中没有
度为1的结点,则一棵有n个叶子结点的哈夫曼树共有2n-1个结点,可以存储在一个大小为2n-1
的一维数组中。树中每个结点还要包含其双亲信息和孩子结点的信息,由此,每个结点的存储结
构设计如图5.27 所示。
weight
parent
lchild
rchild
图5.27 哈夫曼树结点的形式
//-
---
-哈夫曼树的存储表示
typedef struct {
int weight;
int parent,lchild,rchild;
}HTNode *HuffmanTree;
//结点的权值
//结点的双亲、左孩子、右孩子的下标
//动态分配数组存储哈夫曼树
哈夫曼树的各结点存储在由 HuffmanTree 定义的动态分配的数组中,为了实现方便,数组的
0号单元不使用,从1号单元开始使用,所以数组的大小为2n。将叶子结点集中存储在前面部分
1~n 个位置,而后面的n-1 个位置存储其余非叶子结点。
算法 5.10 构造哈夫曼树
【算法步骤】
构造哈夫曼树算法的实现可以分成两大部分。
① 初始化:首先动态申请2n个单元;然后循环2n-1次,从1号单元开始,依次将1至2n-1
所有单元中的双亲、左孩子、右孩子的下标都初始化为0;最后再循环n次,输入前n个单元中
叶子结点的权值。
② 创建树:循环n-1 次,通过n-1次的选择、删除与合并来创建哈夫曼树。选择是从当前
森林中选择双亲为0且权值最小的两个树根结点s1和s2;删除是指将结点s1和2的双亲改为非
0; 合并就是将s1和2的权值和作为一个新结点的权值依次存入到数组的第n+1之后的单元中,
同时记录这个新结点左孩子的下标为s1,右孩子的下标为s2。
【算法描述】
void CreateHuffmanTree (HuffmanTree &HT,int n)
{ //构造哈夫曼树 HT
if(n<=1) return;
m=2*n-1;
HT=new HTNode[m+1];
for(i=1;i<=m;++i)
110号单元未用,所以需要动态分配m+1个单元,HT[m]表示根结点
//将1~m号单元中的双亲、左孩子,右孩子的下标都初始化为0
{HT[i].parent=0;HT[i].lchild=0;HT[i].rchild=0; }
for(i=1;i<=n;++i)
cin>>HT[i].weight;
//输入前n个单元中叶子结点的权值
138
/*-
for(i=n+1;i<=m;++i)
-初始化工作结束,下面开始创建哈夫曼树-
{ //通过n-1 次的选择、删除、合并来创建哈夫曼树
Select (HT, i-1, s1, s2);
//在HT [k] (1≤x≤i-1)中选择两个其双亲域为0且权值最小的结点,并返回它们在HT中的序号 s1 和 s2

Page 147
MG HT[s1].parent=i;HT[s2].parent=i;
//得到新结点,从森林中删除s1,s2,将s1和2的双亲域由0改为i
HT[i].lchild=s1;HT[i].rchild=s2;
HT[i].weight=HT[s1].weight+HT[s2].weight;
}
}
第5章树和二叉树
//s1,s2分别作为的左右孩子
//i的权值为左右孩子权值之和
//for
【例 5.2 】 已知w = (5,29,7,8,14,23,3,11),利用算法 5.10 试构造一棵哈夫曼树,计算树的带
权路径长度,并给出其构造过程中存储结构HT 的初始状态和终结状态。
n = 8,则m = 15,按算法5.10可构造一棵哈夫曼树,如图5.28所示。
23
29
(11
(14)
中擗曼未剑館市
8
拉四曼夫郃(S)
图5.28例5.2的哈夫曼树
树的带权路径长度计算如下:
n
曼大餅
WPL =
= 23 × 2 + 11 × 3+5×4+3×4+29×2+14×3+7×4+8×4= 271
k=1
其存储结构 HT 的初始状态如表5.2(a)所示,其终结状态如表5.2(b)所示。開撕
表5.2
例 5.2 的存储结构
(a)HT 的初态
(b)HT 的终态
结点i weight parent lchild
rchild
结点i
weight parent
lchild rchild


Page 148
140
数据结构(C语言版)(第2版)
哈夫曼树在通信、编码和数据压缩等技术领域有着广泛的应用,下面讨论一个构造通信码的
典型应用——哈夫曼编码。
5.7.3 哈夫曼编码
1. 哈夫曼编码的主要思想
在5.2节提出的案例5.1中已经讨论,在进行数据压缩时,为了使压缩后的数据文件尽可能短,
可采用不定长编码。其基本思想是:为出现次数较多的字符编以较短的编码。为确保对数据文件
进行有效的压缩和对压缩文件进行正确的解码,可以利用哈夫曼树来设计二进制编码。哈夫曼树
的具体构造过程可以根据算法5.10,图5.26所示为图5.4所示的哈夫曼树的构造过程。在图 5.4
所示的哈夫曼树中,约定左分支标记为0,右分支标记为1,则根结点到每个叶子结点路径上的0、
1序列即为相应字符的编码。
下面给出有关编码的两个概念。
(1)前缀编码:如果在一个编码方案中,任一个编码都不是其他任何编码的前缀(最左子串),
则称编码是前缀编码。例如,案例5.1中的第2种编码方案(见表5.1(b))的编码 0,10,110,
111 是前缀编码,而第3种编码方案(见表5.1(c))的编码0,01,010,111 就不是前缀编码。
前缀编码可以保证对压缩文件进行解码时不产生二义性,确保正确解码。
(2)哈夫曼编码:对一棵具有n个叶子的哈夫曼树,若对树中的每个左分支赋予 0,右分支
赋予 1,则从根到每个叶子的路径上,各分支的赋值分别构成一个二进制串,该二进制串就称为
哈夫曼编码。
哈夫曼编码满足下面的两个性质。
性质1 哈夫曼编码是前缀编码。
证明:哈夫曼编码是根到叶子路径上的编码序列,由树的特点知,若路径A是另一条路经 B
的最左部分,则B 经过了A,则A的终点一定不是叶子。而哈夫曼编码对应路径的终点一定为叶
子,因此,任一哈夫曼码都不会与任意其他哈夫曼编码的前缀部分完全重叠,因此哈夫曼编码是
前缀编码。
性质2 哈夫曼编码是最优前缀编码。
对于包括n个字符的数据文件,分别以它们的出现次数为权值构造哈夫曼树,则利用该树对
应的哈夫曼编码对文件进行编码,能使该文件压缩后对应的二进制文件的长度最短。
证明:假设每种字符在数据文件中出现的次数为w,其编码长度为,文件中只有种字符,
则文件总长为 = w 。对应到二叉树上,若置w为叶子结点的权,恰为从根到叶子的路径长度,
i=1
则 w恰为二叉树上带权路径长度。由此可见,设计文件总长最短的二进制前缀编码问题,就
i=1
是以n种字符出现的频率作权设计一棵哈夫曼树的问题。而由哈夫曼树的构造方法可知,出现次
数较多的字符对应的编码较短,这便直观地说明了该定理是成立的。
下面给出根据哈夫曼树构造哈夫曼编码的算法。
2.哈夫曼编码的算法实现
在构造哈夫曼树之后,求哈夫曼编码的主要思想是:依次以叶子为出发点,向上回溯至根结
点为止。回溯时走左分支则生成代码0,走右分支则生成代码 1。

Page 149
地址。
第5章树和二叉树
由于每个哈夫曼编码是变长编码,因此使用一个指针数组来存放每个字符编码串的首
//---- -哈夫曼编码表的存储表示
typedef char **HuffmanCode; // 动态分配数组存储哈夫曼编码表
各字符的哈夫曼编码存储在由 HuffmanCode 定义的动态分配的数组 HC中,为了实现方
便,数组的0号单元不使用,从1号单元开始使用,所以数组 HC 的大小为n+1,即编码表
HC 包括 n+1 行。但因为每个字符编码的长度事先不能确定,所以不能预先为每个字符分配大
小合适的存储空间。为不浪费存储空间,动态分配一个长度为n(字符编码长度一定小于n)
的一维数组 cd,用来临时存放当前正在求解的第i(1≤i≤n)个字符的编码,当第i个字符的
编码求解完毕后,根据数组 cd 的字符串长度分配 HC[i]的空间,然后将数组 cd中的编码复制
到 HC[i]中。
因为求解编码时是从哈夫曼树的叶子出发,向上回溯至根结点。所以对于每个字符,得到的
编码顺序是从右向左的,故将编码向数组 cd 存放的顺序也是从后向前的,即每个字符的第1个编
码存放在 cd[n-2]中(cd[n-1]存放字符串结束标志\0'),第2个编码存放在cd[n-3]中,依此类推,
直到全部编码存放完毕。
算法5.11 根据哈夫曼树求哈夫曼编码
【算法步骤】
48010
① 分配存储n个字符编码的编码表空间HC,长度为+1;分配临时存储每个字符编码的动
态数组空间 cd,cd[n-1]置为'\0'。
②逐个求解n个字符的编码,循环n次,执行以下操作:
● 设置变量 start 用于记录编码在cd中存放的位置,start 初始时指向最后,即编码结束
符位置 n-1;
设置变量用于记录从叶子结点向上回溯至根结点所经过的结点下标,c初始时为当
前待编码字符的下标i,f用于记录i的双亲结点的下标;
● 从叶子结点向上回溯至根结点,求得字符i的编码,当f没有到达根结点时,循环执
行以下操作:
回溯一次 start 向前指一个位置,即--start;
▶ 若结点c是f的左孩子,则生成代码0,否则生成代码1,生成的代码0或1保存
在 cd[start]中;
继续向上回溯,改变c和f的值。
政
根据数组 cd 的字符串长度为第i个字符编码分配空间 HC[i],然后将数组 cd 中的编码
复制到 HC[i]中。
③ 释放临时空间 cd。
【算法描述】
int n):
void CreatHuffmanCode (HuffmanTree HT,HuffmanCode &HC,int n)
{//从叶子到根逆向求每个字符的哈夫曼编码,存储在编码表 HC 中
HC=new char*[n+1];
cd=new char[n];
cd[n-1]='\0';
for(i=1;i<=n;++i)
品名: //分配存储n个字符编码的编码表空间
//分配临时存放每个字符编码的动态数组空间
//编码结束符
//逐个字符求哈夫曼编码
141

Page 150
142
数据结构(C语言版)(第2版)
start=n-1;
c=i;f=HT[i].parent;
while(f!=0)
{
//start 开始时指向最后,即编码结束符位置
//f指向结点c的双亲结点
//从叶子结点开始向上回溯,直到根结点
TU-start;
//回溯一次start 向前指一个位置
if(HT[f].lchild==c) cd[start]='0';
//结点cf的左孩子,则生成代码0
else cd[start]='1';
//结点c是f的右孩子,则生成代码1
c=f;f=HT[f].parent;
//继续向上回溯
//求出第i个字符的编码
( HC[i]=new char[n-start];
//为第i个字符编码分配空间
strcpy(HC[i], &cd[start]);
//将求得的编码从临时空间cd复制到HC的当前行中
//for
delete cd;
}
//释放临时空间
【例5.3】 已知某系统在通信联络中只可能出现8种字符,其概率分别为0.05,0.29,0.07,
0.08, 0.14,0.23, 0.03,0.11,试设计哈夫曼编码。
根据其出现的概率可设8个字符的权值为:w=(5,29,7,8,14,23,3,11),其对应的哈夫曼树如图5.28
所示。将树的左分支标记为0,右分支标记为1,便得到其哈夫曼编码表如图5.29 所示。

图5.29 前缀编码表
3. 文件的编码和译码
(1)编码
有了字符集的哈夫曼编码表之后,对数据文件的编码过程是:依次读入文件中的字符c,在
哈夫曼编码表 HC中找到此字符,将字符c转换为编码表中存放的编码串。
(2)译码
对编码后的文件进行译码的过程必须借助于哈夫曼树。具体过程是:依次读入文件的二进制
码,从哈夫曼树的根结点(即HT[m])出发,若当前读入0,则走向左孩子,否则走向右孩子。
一旦到达某一叶子HT[i]时便译出相应的字符编码 HC[i]。然后重新从根出发继续译码,直至文件
结束。
具体编码和译码的算法留给读者去完成。

## 七、案例分析与实现

在5.2节引入的案例5.1已在5.7节中进行了详细的讨论,案例5.2提出了可以利用二叉树来
表示表达式,本节将对案例5.2作进一步的分析,给出利用表达式构建表达式树和利用表达式树
求解表达式的算法。
案例 5.2:利用二叉树求解表达式的值。
【案例分析】
对于任意一个算术表达式,都可用二叉树来表示。表达式对应的二叉树创建后,利用二叉树
的遍历等操作,很容易实现表达式的求值运算。因此问题的关键就是如何创建表达式树,下面讨
论由中缀表达式创建表达式树的方法。
假设运算符均为双目运算符,则表达式对应的表达式树中叶子结点均为操作数,分支结点均
为运算符。由于创建的表达式树需要准确的表达运算次序,因此在扫描表达式创建表达式树的过
程中,当遇到运算符时不能直接创建结点,而应将其与前面的运算符进行优先级比较,根据比较
的结果再进行处理。这种处理方式类似于第3章的表达式求值算法中的运算符的比较,可以借助
一个运算符栈,来暂存已经扫描到的还未处理的运算符。
根据表达式树与表达式对应关系的递归定义,每两个操作数和一个运算符就可以建立一
棵表达式二叉树,而该二叉树又可以作为另一个运算符结点的一棵子树。可以另外借助一个
表达式树栈,来暂存已建立好的表达式树的根结点,以便其作为另一个运算符结点的子树而
被引用。
【案例实现】
为实现表达式树的创建算法,可以使用两个工作栈,一个称做OPTR,用以暂存运算符;另
一个称做 EXPT,用以暂存已建立好的表达式树的根结点。
为了便于实现,和第3章一样,假设每个表达式均以“#”开始,以“#”结束。
算法5.12 表达式树的创建
【算法步骤】
① 初始化 OPTR栈和EXPT栈,将表达式起始符“#”压入 OPTR 栈。
② 扫描表达式,读入第一个字符ch,如果表达式没有扫描完毕至“#”或OPTR 的栈顶元素
不为“#”时,则循环执行以下操作:国米米学个国甲大多
● 若ch不是运算符,则以ch为根创建一棵只有根结点的二叉树,且将该树根结点压入
EXPT栈,读入下一字符 ch;
● 若ch是运算符,则根据OPTR的栈顶元素和ch的优先级比较结果,做不同的处理:
若是小于,则ch压入OPTR栈,读入下一字符ch;
若是大于,则弹出 OPTR 栈顶的运算符,从EXPT 栈弹出两个表达式子树的根
结点,以该运算符为根结点,以EXPT 栈中弹出的第二个子树作为左子树,以
EXPT 栈中弹出的第一个子树作为右子树,创建一棵二叉树,并将该树根结点
压入EXPT栈;
若是等于,则 OPTR的栈顶元素是“(”且ch是“)”,这时弹出 OPTR栈顶的“(”,
相当于括号匹配成功,然后读入下一字符 ch。

【算法描述】
void InitExpTree ()
{//表达式树的创建算法
InitStack (EXPT);
InitStack (OPTR);
Push (OPTR,'#');
cin>>ch;
while(ch!='#' | |GetTop (OPTR)!='#')
{
if(!In(ch))
{
}
else
CreateExpTree (T,NULL,NULL, ch);
Push (EXPT, T);
cin>>ch;
//初始化 EXPT 栈
//初始化 OPTR 栈
//将表达式起始符“#”压入OPTR栈
//表达式没有扫描完毕或OPTR的栈顶元素不为“#”
//ch不是运算符
//以ch 为根创建一棵只有根结点的二叉树
//将二叉树根结点T进EXPT栈
//读入下一字符
switch (Precede (GetTop (OPTR), ch))
//比较OPTR的栈顶元素和ch的优先级
{
case '<':
Push (OPTR, ch);cin>>ch;
break;
case '>':
Pop (OPTR, theta);
Pop (EXPT,b); Pop (EXPT,a);
CreateExpTree(T,a,b,theta)
//当前字符ch 压入OPTR栈,读入下一字符
//弹出 OPTR栈顶的运算符
//弹出 EXPT 栈顶的两个运算数
//以 theta 为根,a为左子树,b为右子树,创建一棵二叉树
Push (EXPT, T);
break;
case '='
Pop (OPTR,x);cin>>ch;
break;
}
//将二叉树根结点T进EXPT栈
//OPTR 的栈顶元素是“(”且ch是“)”
//弹出 OPTR 栈顶的“(”,读入下一字符 ch
//switch
【算法分析】
//while
此算法从头到尾扫描表达式中每个字符,若表达式的字符串长度为n,则此算法的时间复杂
度为O(n)。算法在运行时所占用的辅助空间主要取决于OPTR 栈和EXPT栈的大小,显然,它们
的空间大小之和不会超过n,所以此算法的空间复杂度也同样为O(n)。
算法5.13 表达式树的求值
【算法步骤】
① 设变量 Ivalue 和rvalue 分别用以记录表达式树中左子树和右子树的值,初始均为0。
② 如果当前结点为叶子结点为操作数),则返回该结点的数值,否则(结点为运算符)执
行以下操作:
递归计算左子树的值记为lvalue;
递归计算右子树的值记为rvalue;
● 根据当前结点运算符的类型,将lvalue 和 rvalue 进行相应运算并返回。