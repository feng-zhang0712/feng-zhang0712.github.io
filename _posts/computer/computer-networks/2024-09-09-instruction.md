---
layout: post
title: 计算机网络 - 概述
categories: computer
tags:
  - computer
  - networks
---

## 一、互联网概述

**计算机网络**（简称为**网络**）由若干**结点**（node）和连接这些结点的**链路**（link）组成。网络中的结点可以是计算机、集线器、交换机或路由器等。

**网络把许多计算机连接在一起，而互连网则把许多网络通过路由器连接在一起。与网络相连的计算机常称为主机。**

互联网服务由互联网服务提供商ISP （Internet Service Provider）提供，根据提供服务的覆盖面积大小以及所拥有的 IP 地址数目的不同，ISP 分为不同的层次：**主干 ISP**、**地区 ISP**和**本地 ISP**。

- **主干 ISP**：由几个专门的公司创建和维持，服务面积最大（一般都能够覆盖国家范围），并且还拥有高速主干网。有些地区 ISP 网络也可直接与主干 ISP 相连。
- **地区 ISP**：是一些较小的 ISP。这些地区 ISP 通过一个或多个主干 ISP 连接起来。它们位于等级中的第二层，数据率也低一些。
- **本地 ISP**：给用户提供直接的服务（这些用户有时也称为端用户，强调是末端的用户）。本地 ISP 可以连接到地区 ISP，也可直接连接到主干 ISP。绝大多数的用户都是连接到本地 ISP 的。本地 ISP 可以是一个仅仅提供互联网服务的公司，也可以是一个拥有网络并向自己的雇员提供服务的企业，或者是一个运行自己的网络的非营利机构（如学院或大学）。本地 ISP 可以与地区 ISP 或主干 ISP 连接。

![基于 ISP 的多层结构的互联网的概念示意图]({{ site.baseurl }}/assets/images/computer-networks/isp-based-multi-level-internet.png)
*基于 ISP 的多层结构的互联网的概念示意图*

从原理上讲，只要每个本地 ISP 都安装了路由器连接到某个地区 ISP，而每个地区 ISP 也有路由器连接到主干 ISP，那么在这些相互连接的 ISP 的共同合作下，就可以完成互联网中的所有的分组转发任务。随着互联网上数据流量的急剧增长，人们开始研究如何更快地转发分组，以及如何更加有效地利用网络资源。于是，**互联网交换点 IXP**（Internet Exchange Point）应运而生。

互联网交换点 IXP 的主要作用就是允许两个网络直接相连并交换分组，而不需要再通过第三个网络来转发分组。例如，在上图中，右方的两个地区 ISP 通过一个 IXP 连接起来。这样，主机 `A` 和主机 `B` 交换分组时，就不必再经过最上层的主干 ISP，而是直接在两个地区 ISP 之间用高速链路对等地交换分组。这样就使互联网上的数据流量分布更加合理，同时也减少了分组转发的迟延时间，降低了分组转发的费用。IXP 常采用工作在数据链路层的网络交换机，这些网络交换机都用局域网互连起来。

## 二、互联网的组成

从互联网的工作方式看，可以划分为以下两大块。

- **边缘部分**：由所有连接在互联网上的主机组成。这部分是用户直接使用的，用来进行通信和资源共享。
- **核心部分**：由大量网络和连接这些网络的路由器组成。这部分是为边缘部分提供服务的（提供连通性和交换）。

![互联网的边缘部分与核心部分]({{ site.baseurl }}/assets/images/computer-networks/edge-and-core-of-the-internet.png)
*互联网的边缘部分与核心部分*

### 2.1 互联网的边缘部分

处在互联网边缘的部分就是连接在互联网上的所有的主机。这些主机又称为**端系统**（end system），即互联网的末端。小的端系统可以是一台普通个人电脑和具有上网功能的智能手机，甚至是一个很小的网络摄像头，而大的端系统则可以是一台非常昂贵的大型计算机。边缘部分利用核心部分所提供的服务，使众多主机之间能够互相通信交换或共享信息。

此外，当我们说：“主机 `A` 和主机 `B` 进行通信”，实际上是指：“运行在主机 `A` 上的某个程序和运行在主机 `B` 上的另一个程序进行通信”。由于“进程”就是“运行着的程序”，因此这也就是指：“**主机 `A` 的某个进程和主机 `B` 上的另一个进程进行通信**”。

在网络边缘的端系统之间的通信方式通常可划分为两大类：**客户-服务器方式**（C/S 方式）和**对等方式**（P2P 方式）。

#### （1）客户服务器方式

这种方式在互联网上是最常用的，也是传统的方式。我们在上网发送电子邮件或在网站上查找资料时，都是使用客户-服务器方式。采用客户-服务器方式可以使两个应用进程进行通信。客户（client）和服务器（server）都是指通信中所涉及的两个应用进程。客户-服务器方式所描述的是进程之间服务和被服务的关系。**客户是服务请求方，服务器是服务提供方**。

在实际应用中，客户程序和服务器程序通常还具有以下特点。

**客户程序**：

- 被用户调用后运行，在通信时主动向远地服务器发起通信（请求服务）。因此，客户程序必须知道服务器程序的地址。
- 不需要特殊的硬件和很复杂的操作系统。

**服务器程序**：

- 是一种专门用来提供某种服务的程序，可同时处理多个远地或本地客户的请求。
- 系统启动后即自动调用并一直不断地运行，被动地等待并接受来自各地的客户的通信请求。因此，服务器程序不需要知道客户程序的地址。
- 一般需要有强大的硬件和高级的操作系统支持。

客户与服务器的通信关系建立后，通信可以是双向的，客户和服务器都可发送和接收数据。

#### （2）对等连接方式

**对等连接**（peer-to-peer，简写为 **P2P**）是指两台主机在通信时并不区分哪个是服务请求方哪个是服务提供方。只要两台主机都运行了对等连接软件（P2P 软件），它们就可以进行平等的、对等连接通信。

### 2.2 互联网的核心部分

网络中的核心部分要向网络边缘中的大量主机提供连通性，使边缘部分中的任何一台主机都能够向其他主机通信。网络核心部分起特殊作用的是**路由器**（router）。路由器是实现**分组交换**（packet switching）的关键构件，其任务是**转发收到的分组**，这是网络核心部分最重要的功能。为了弄清分组交换，下面先介绍电路交换的基本概念。

#### （1）电路交换的主要特点

在电话问世后不久，人们就发现，要让所有的电话机都两两相连接是不现实的。下图（a）表示两部电话只需要用一对电线就能够互相连接起来。但若有 5 部电话要两两相连，则需要 10 对电线，如下图（b）所示。显然，若 `N` 部电话要两两相连，就需要 `N(N-1)/2` 对电线。当电话机的数量很大时，这种连接方法需要的电线数量就太大了。要使得每部电话能够很方便地和另一部电话进行通信，应当使用电话交换机将这些电话连接起来，如下图（c）所示。每部电话都连接到交换机上，而交换机使用交换的方法，让电话用户彼此之间可以很方便地通信。电话发明后的一百多年来，电话交换机虽然经过多次更新换代，但交换的方式一直都是**电路交换**（circuit switching）。

![电话机的不同连接方法]({{ site.baseurl }}/assets/images/computer-networks/phone-connection-methods.png)
*电话机的不同连接方法*

从通信资源的分配角度来看，**交换**（switching）就是按照某种方式动态地分配传输线路的资源。在使用电路交换通话时，从主叫端到被叫端就建立了一条连接，也就是一条专用的物理通路。这条连接保证了双方通话时所需的通信资源，而这些资源在双方通信时不会被其他用户占用。此后主叫和被叫双方就能互相通电话。通话完毕挂机后，交换机释放刚才使用的这条专用的物理通路。这种必须经过**建立连接**（占用通信资源）$\to$ **通话**（一直占用通信资源）$\to$ **释放连接**（归还通信资源）三个步骤的交换方式称为**电路交换**。

下图为电路交换的示意图。电路交换的一个重要特点就是**在通话的全部时间内，通话的两个用户始终占用端到端的通信资源**。

![电路交换的用户始终占用端到端的通信资源]({{ site.baseurl }}/assets/images/computer-networks/circuit-switching-example.png)
*电路交换的用户始终占用端到端的通信资源*

使用电路交换传送计算机数据时，其线路的传输效率往往很低。这是因为计算机数据是突发式地出现在传输线路上的，因此线路上真正用来传送数据的时间往往不到 10% 甚至 1%。已被用户占用的通信线路资源在绝大部分时间里都是空闲的。

#### （2）分组交换的主要特点

分组交换则采用**存储转发**技术。下图表示把一个报文划分为几个分组后再进行传送。通常我们把要发送的整块数据称为一个**报文**（message）。在发送报文之前，先把较长的报文划分为一个个更小的等长数据段。例如，每个数据段为 1024 bit。在每个数据段前面，加上一些由必要的控制信息组成的**首部**（header）后，就构成了一个**分组**（packet）。分组又称为“**包**”，而分组的首部也可称为“**包头**”。分组是在互联网中传送的数据单元。分组的首部包含了诸如目的地址和源地址等重要控制信息，使得分组能在互联网中独立地选择传输路径，并被正确地交付到分组传输的终点。

![以分组为基本单位在网络中传送]({{ site.baseurl }}/assets/images/computer-networks/packet-based-transition.png)
*以分组为基本单位在网络中传送*

下图中的（a）强调互联网的核心部分由许多网络和把它们互连起来的路由器组成的，而主机处在互联网的边缘部分。在互联网核心部分的路由器之间一般都用高速链路相连接，而在网络边缘的主机接入到核心部分则通常以相对较低速率的链路相连接。

**位于网络核心部分的路由器用来转发分组，即进行分组交换**。路由器收到一个分组，先暂时存储，检查其首部，查找转发表，按照首部中的目的地址，找到合适的接口转发出去，把分组交给下一个路由器。这样一步步地以存储转发的方式，把分组交付到最终的目的主机。各路由器之间必须经常交换彼此掌握的路由信息，以便创建和动态维护路由器中的转发表，使得转发表能够在整个网络拓扑发生变化时及时更新。

![分组交换的示意图]({{ site.baseurl }}/assets/images/computer-networks/packet-switching-diagram.png)
*分组交换的示意图*

现在假定上图（b）中的主机 $H_1$ 向主机 $H_5$ 发送数据。主机 $H_1$ 先将分组逐个发往与它直接相连的路由器 `A`。此时，除链路 $H_1$-A 外，其他通信链路并不被目前通信的双方所占用。需要注意的是，即使是链路 $H_1$-A，也只是当分组正在此链路上传送时才被占用。在各分组传送之间的空闲时间，链路 $H_1$-A 仍可为其他主机发送的分组使用。

路由器 `A` 把主机 $H_1$ 发来的分组放入缓存。假定从路由器 `A` 的转发表中查出应把该分组转发到链路 A-C。于是分组就传送到路由器 `C`。当分组正在链路 A-C 传送时，该分组并不占用网络其他部分的资源。路由器 `C` 继续按上述方式查找转发表，假定查出应转发到路由器 `E`。当分组到达路由器 `E` 后，路由器 `E` 就最后把分组直接交给主机 $H_5$。

假定在某个分组的传送过程中，链路 A-C 的通信量太大，那么路由器 `A` 可以把分组沿另一个路由传送，即先转发到路由器 `B`，再转发到路由器 `E`，最后把分组送到主机 $H_5$。在网络中可同时有多台主机进行通信，如主机 $H_2$ 也可以经过路由器 `B` 和 `E` 与主机 $H_6$ 通信。

注意，路由器暂时存储的是一个个短分组，而不是整个的长报文。短分组是暂存在路由器的存储器（即内存）中而不是存储在磁盘中的。这就保证了较高的交换速率。此外，分组交换在传送数据之前不必先占用一条端到端的链路的通信资源。分组在哪段链路上传送才占用这段链路的通信资源。分组到达一个路由器后，先暂时存储下来，查找转发表，然后从一条合适的链路转发出去。分组在传输时就这样一段一段地断续占用通信资源，而且还省去了建立连接和释放连接的开销，因而数据的传输效率更高。

互联网采取了专门的措施，保证了数据的传送具有非常高的可靠性。当网络中的某些结点或链路突然出现故障时，在各路由器中运行的路由选择**协议**（protocol）能够自动找到转发分组最合适的路径。

从以上所述可知，采用存储转发的分组交换，实质上是采用了在数据通信的过程中断续（或动态）分配传输带宽的策略。这对传送突发式的计算机数据非常合适，使得通信线路的利用率大大提高。

为了提高分组交换网的可靠性，互联网的核心部分常采用网状拓扑结构，使得当发生网络拥塞或少数结点、链路出现故障时，路由器可灵活地改变转发路由而不致引起通信的中断或全网的瘫痪。此外，通信网络的主干线路往往由一些高速链路构成，这样就可以以较高的数据率迅速地传送计算机数据。

| 优点 | 所采用的手段 |
| :--------: | :-------: |
| 高效 | 在分组传输的过程中动态分配传输带宽，对通信链路是逐段占用 |
| 灵活 | 为每一个分组独立地选择最合适的转发路由 |
| 迅速 | 以分组作为传送单位，可以不先建立连接就能向其他主机发送分组 |
| 可靠 | 保证可靠性的网络协议;分布式多路由的分组交换网，使网络有很好的生存性 |

分组交换也带来一些新的问题。

- 分组在各路由器存储转发时需要排队，这就会造成一定的**时延**。因此，必须尽量设法减少这种时延。此外，由于分组交换不像电路交换那样通过建立连接来保证通信时所需的各种资源，因而无法确保通信时端到端所需的带宽。
- 分组交换带来的另一个问题是各分组必须携带的控制信息也造成了一定的**开销**（overhead）。整个分组交换网还需要专门的管理和控制机制。

下图表示电路交换、报文交换和分组交换的主要区别。图中的 `A` 和 `D` 分别是源点和终点，而 `B` 和 `C` 是在 `A` 和 `D` 之间的中间结点。图中的最下方归纳了三种交换方式在数据传送阶段的主要特点。

- **电路交换**：整个报文的比特流连续地从源点直达终点，好像在一个管道中传送。
- **报文交换**：整个报文先传送到相邻结点，全部存储下来后查找转发表，转发到下个结点。
- **分组交换**：单个分组（这只是整个报文的一部分）传送到相邻结点，存储下来后查找转发表，转发到下个结点。

![三种交换的比较]({{ site.baseurl }}/assets/images/computer-networks/difference-of-switching.png)
*三种交换的比较。电路交换、报文交换、分组交换，$P_1$~$P_4$ 表示 4 个分组*

从上图可看出，若要连续传送大量的数据，且其传送时间远大于连接建立时间，则电路交换的传输速率较快。报文交换和分组交换不需要预先分配传输带宽，在传送突发数据时可提高整个网络的信道利用率。由于一个分组的长度往往远小于整个报文的长度，因此分组交换比报文交换的时延小，同时也具有更好的灵活性。

## 三、计算机网络的性能

### 3.1 计算机网络的性能指标

性能指标从不同的方面来度量计算机网络的性能。下面介绍常用的七个性能指标。

#### （1）速率

**比特**（bit）来源于 binary digit，意思是一个“二进制数字”，因此一个比特就是二进制数字中的一个 1 或 0。网络技术中的速率指的是**数据的传送速率**，它也称为**数据率**（data rate）或**比特率**（bit rate）。速率是计算机网络中最重要的一个性能指标。速率的单位是 `bit/s`（比特每秒）（或 b/s，有时也写为 bps，即 bit per second）。当数据率较高时，常常在 bit/s 的前面加上一个字母。例如：

- **k**（kilo）= $10^3$ = 千
- **M**（Mega）= $10^6$ = 兆
- **G**（Giga）= $10^9$ = 吉
- **T**（Tera）= $10^12$ = 太
- **P**（Peta）= $10^15$ = 拍
- **E**（Exa）= $10^18$ = 艾
- **Z**（Zetta）= $10^21$ = 泽
- **Y**（Yotta）= $10^24$ = 尧

注意，当提到网络的速率时，往往指的是**额定速率**或**标称速率**，并非网络实际上运行的速率。

#### （2）带宽

**带宽**（bandwidth）有以下两种不同的意义：

- 带宽本来是指某个**信号具有的频带宽度**。信号的带宽是指该信号所包含的各种不同频率成分所占据的频率范围。例如，在传统的通信线路上传送的电话信号的标准带宽是 3.1kHz（从 300Hz 到 3.4kHz，即话音的主要成分的频率范围）。这种意义的带宽的单位是**赫**（或千赫、兆赫、吉赫等）。在过去很长的一段时间，通信的主干线路传送的是模拟信号（即连续变化的信号）。因此，表示某信道允许通过的信号频带范围就称为该信道的**带宽**（或**通频带**）。
- 在计算机网络中，带宽用来表示网络中某**通道传送数据的能力**，因此网络带宽表示在单位时间内网络中的某信道所能通过的**最高数据率**。这种意义的带宽的单位就是数据率的单位 bit/s，是“比特每秒”。

在“带宽”的上述两种表述中，前者为**频域**称谓，后者为**时域**称谓，其本质是相同的。也就是说，一条通信链路的“带宽”越宽，其所能传输的“最高数据率”也越高。

#### （3）吞吐量

**吞吐量**（throughput）表示在单位时间内通过某个网络（或信道、接口）的实际的数据量。吞吐量经常用于对现实世界中网络的测量，以便知道实际上到底有多少数据量能够通过网络。显然，吞吐量受网络的带宽或网络的额定速率的限制。例如，对于一个 `1Gbit/s` 的以太网，就是说其额定速率是 `1Gbit/s`，那么这个数值也是该以太网的吞吐量的绝对上限值。因此，对 `1Gbit/s` 的以太网，其实际的吞吐量可能也只有 `100Mbit/s`，或甚至更低，并没有达到其额定速率。

#### （4）时延

**时延**（delay）是指数据（一个报文或分组，甚至比特）从网络（或链路）的一端传送到另一端所需的时间。时延也称为延迟或迟延。网络中的时延由以下几个不同的部分组成：

- **发送时延**（transmission delay）：是**主机或路由器发送数据帧所需要的时间**，也就是从发送数据帧的第一个比特算起，到该帧的最后一个比特发送完毕所需的时间。发送时延的计算公式是：

  $$发送时延 = \frac{数据帧长度(bit)}{发送速率(bit/s)}$$

  对于一定的网络，发送时延并非固定不变，而是与发送的帧长（单位是比特）成正比，与发送速率成反比。发送时延发生在机器内部的发送器中（一般就是发生在网络适配器中）与传输信道的长度（或信号传送的距离）没有任何关系。
- **传播时延**（propagation delay）：是**电磁波在信道中传播一定的距离需要花费的时间**。传播时延的计算公式是：

  $$传播时延 = \frac{信道长度(m)}{电磁波在信道上的传播速率(m/s)}$$

  电磁波在自由空间的传播速率是光速，即 $3.0×10^5$ km/s。电磁波在网络传输媒体中的传播速率比在自由空间要略低一些：在铜线电缆中的传播速率约为 $2.3×10^5$ km/s，在光纤中的传播速率约为 $2.0 × 10^5$ km/s。

  传播时延发生在机器外部的传输信道媒体上，与信号的发送速率无关。信号传送的距离越远，传播时延就越大。
- **处理时延**：主机或路由器在收到分组时要花费一定的时间进行处理，例如分析分组的首部、从分组中提取数据部分、进行差错检验或查找适当的路由等，这就产生了处理时延。
- **排队时延**：分组在经过网络传输时，要经过许多路由器。但分组在进入路由器后要先在输入队列中排队等待处理。在路由器确定了转发接口后，还要在输出队列中排队等待转发。这就产生了排队时延。排队时延的长短往往取决于网络当时的通信量。当网络的通信量很大时会发生队列溢出，使分组丢失，这相当于排队时延为无穷大。

这样，数据在网络中经历的总时延就是以上四种时延之和：

$$总时延 = 发送时延 + 传播时延 + 处理时延 + 排队时延$$

一般说来，小时延的网络要优于大时延的网络。在某些情况下，一个低速率、小时延的网络很可能要优于一个高速率但大时延的网络。

![几种时延产生的地方不一样]({{ site.baseurl }}/assets/images/computer-networks/delay-diagram.png)
*几种时延产生的地方不一样*

必须指出，在总时延中，究竟哪种时延占主导地位，必须具体分析。我们知道，汽车在路面质量很好的高速公路上可明显地提高行驶速率。然而对于高速网络链路，我们提高的仅仅是**数据的发送速率而不是比特在链路上的传播速率**。荷载信息的电磁波在通信线路上的传播速率（这是光速的数量级）取决于通信线路的介质材料，而与数据的发送速率并无关系。**提高数据的发送速率只是减小了数据的发送时延**。此外，数据的发送速率的单位是每秒发送多少个比特，这是指在**某个点**或**某个接口**上的发送速率。而传播速率的单位是每秒传播多少公里，是指在某一段传输线路上比特的传播速率。因此，通常所说的“光纤信道的传输速率高”是指可以用很高的速率向光纤信道发送数据，而光纤信道的传播速率实际上还要比铜线的传播速率略低一点，光在光纤中的传播速率约为每秒 20.5 万公里，它比电磁波在铜线中的传播速率（每秒 23.1 万公里）略低一些。

#### （5）时延带宽积

传播**时延带宽积**是传播时延和带宽相乘的结果，即：

$$时延带宽积 = 传播时延 * 带宽$$

我们可以用如下示意图来表示时延带宽积。这是一个代表链路的圆柱形管道，管道的长度是链路的传播时延（请注意，现在以时间作为单位来表示链路长度），而管道的截面积是链路的带宽。因此时延带宽积就表示这个管道的体积，表示这样的链路可容纳多少个比特。例如，设某段链路的传播时延为 `20ms`，带宽为 `10Mbit/s`。算出

$$时延带宽积 = 20×10^{-3} × 10 × 10^6 = 2 × 10^5 bit$$

这就表明，若发送端连续发送数据，则在发送的第一个比特即将达到终点时，发送端就已经发送了 20 万个比特，而这 20 万个比特都正在链路上向前移动。因此，链路的时延带宽积又称为**以比特为单位的链路长度**。

![链路像一条空心管道]({{ site.baseurl }}/assets/images/computer-networks/delay-bandwidth.png)
*链路像一条空心管道*

不难看出，管道中的比特数表示从发送端发出的但尚未到达接收端的比特。对于一条正在传送数据的链路，只有在代表链路的管道都充满比特时，链路才得到充分的利用。

#### （6）往返时间 RTT

在许多情况下，互联网上的信息不仅仅单方向传输而是双向交互的。因此，我们有时很需要知道双向交互一次所需的时间，而这个时间，就是**往返时间 RTT**（Round-Trip Time）。例如，`A` 向 `B` 发送数据。如果数据长度 100MB，发送速率是 100 Mbit/s，那么：

$$发送时间 = \frac{数据长度}{发送速率} = \frac{100 × 2^{20} × 8}{100 × 10^6} \approx 8.39s $$

如果 B 正确收完 100MB 的数据后，就立即向 A 发送确认。再假定 A 只有在收到 B 的确认信息后，才能继续向 B 发送数据。显然，这需要等待一个往返时间 RTT（这里假定确认信息很短，可忽略 B 发送确认的时间）。如果 RTT = 2s，那么可以算出 A 向 B 发送数据的有效数据率。

$$有效数据率 = \frac{数据长度}{发送时间 + RTT} = \frac{100 × 2^{20} \times 8}{8.39 + 2} \approx 80.7 × 10^6 bit/s \approx 80.7 Mbit/s$$

比原来的数据率 100 Mbit/s 小不少。
在互联网中，往返时间还包括各中间结点的处理时延、排队时延以及转发数据时的发送时延。当使用卫星通信时，往返时间 RTT 相对较长，是很重要的一个性能指标。

#### （7）利用率

**利用率**有信道利用率和网络利用率两种。

- 信道利用率指出某信道有百分之几的时间是被利用的（有数据通过）。完全空闲的信道的利用率是零。
- 网络利用率是全网络的信道利用率的加权平均值。

信道利用率并非越高越好。这是因为，根据排队论的理论，当某信道的利用率增大时，该信道引起的时延也就迅速增加。这和高速公路的情况有些相似。当高速公路上的车流量很大时，由于在公路上的某些地方会出现堵塞，因此行车所需的时间就会变长。网络也有类似的情况。当网络的通信量很少时，网络产生的时延并不大。但在网络通信量不断增大的情况下，由于分组在网络结点（路由器或结点交换机）进行处理时需要排队等候，因此网络引起的时延就会增大。如果令 $D_0$ 表示网络空闲时的时延，D 表示网络当前的时延，那么在适当的假定条件下，可以用下面的简单公式来表示 D、$D_0$ 和利用率 U 之
间的关系：

$$D = \frac{D_0}{1-U}$$

这里 U 是网络的利用率，数值在 0 到 1 之间。当网络的利用率达到其容量的 1/2 时，时延就要加倍。值得注意的是：当网络的利用率接近最大值 1 时，网络的时延就趋于无穷大。因此我们必须有这样的概念：信道或网络的利用率过高会产生非常大的时延。如下图所示。因此一些拥有较大主干网的 ISP 通常控制信道利用率不超过 50%。如果超过了就要准备扩容，增大线路的带宽。

![时延与利用率的关系]({{ site.baseurl }}/assets/images/computer-networks/relationship-between-delay-and-usage-rate.png)
*时延与利用率的关系*

### 3.2 计算机网络的非性能特征

计算机网络还有一些非性能特征也很重要。这些非性能特征与前面介绍的性能指标有很大的关系。比如：费用、质量、标准化、可靠性、可扩展性和可升级性等。

## 四、计算机网络体系结构

### 4.1 计算机网络体系结构的形成

互联网诞生的早期，由于网络体系结构的不同，不同公司的设备很难互相连通。为了使不同体系结构的计算机网络都能互连，国际标准化组织 ISO 提出了一个试图使各种计算机在世界范围内互连成网的标准框架，即著名的**开放系统互连基本参考模型 OSI/RM**（Open Systems Interconnection Reference Model），简称为 OSI。事实上，由于 OSI 标准存在很多问题，此标准并未得到实施。

为进行网络中的数据交换而建立的规则、标准或约定称为**网络协议**（network protocol）（简称协议）。网络协议主要由以下三个要素组成：

- **语法**：即数据与控制信息的结构或格式。
- **语义**：即需要发出何种控制信息，完成何种动作以及做出何种响应。
- **同步**：即事件实现顺序的详细说明。

对于非常复杂的计算机网络协议，其结构应该是层次式的。分层可以带来很多好处。比如：

- 各层之间相互独立：某一层并不需要知道它的下一层是如何实现的，而仅仅需要知道该层通过层间的接口（即界面）所提供的服务。由于每一层实现一种相对独立的功能，因而可将一个难以处理的复杂问题分解为若干个较容易处理的更小一些的问题。这样，整个问题的复杂程度就下降了。
- 灵活性好：当任何一层发生变化时（例如由于技术的变化），只要层间接口关系保持不变，则在这层以上或以下各层均不受影响。此外，对某一层提供的服务还可进行修改。当某层提供的服务不再需要时，甚至可以将这层取消。
- 结构上可分割开：各层都可以采用最合适的技术来实现。
- 易于实现和维护：这种结构使得实现和调试一个庞大而又复杂的系统变得易于处理，因为整个的系统已被分解为若干个相对独立的子系统。
- 能促进标准化工作：因为每一层的功能及其所提供的服务都已有了精确的说明。

通常各层所要完成的功能主要有以下一些（可以只包括一种，也可以包括多种）：

- 差错控制：使相应层次对等方的通信更加可靠。
- 流量控制：发送端的发送速率必须使接收端来得及接收，不要太快。
- 分段和重装：发送端将要发送的数据块划分为更小的单位，在接收端将其还原。
- 复用和分用：发送端几个高层会话复用一条低层的连接，在接收端再进行分用。
- 连接建立和释放：交换数据前先建立一条逻辑连接，数据传送结束后释放连接。

分层当然也有一些缺点，例如，有些功能会在不同的层次中重复出现，因而产生额外开销。

**计算机网络的各层及其协议的集合**就是网络的**体系结构**（architecture）。换种说法，**计算机网络的体系结构就是这个计算机网络及其构件所应完成的功能的精确定义**。需要强调的是：这些功能究竟是用何种硬件或软件完成的，则是一个遵循这种体系结构的实现（implementation）的问题。体系结构是抽象的，而实现则是具体的，是真正在运行的计算机硬件和软件。

### 4.2 具有五层协议的体系结构

OSI 的七层协议体系结构（如下图（a））的概念清楚，理论也较完整，但它既复杂又不实用。TCP/IP 体系结构则不同，但它现在却得到了非常广泛的应用。TCP/IP 是一个四层的体系结构（如下图（b）），它包含应用层、运输层、网际层和网络接口层。不过从实质上讲，TCP/IP 只有最上面的三层，因为最下面的网络接口层并没有什么具体内容。因此在学习计算机网络的原理时往往采取折中的办法，即综合 OSI 和 TCP/IP 的优点，采用一种只有五层协议的体系结构（如下图（c）），这样既简洁又能将概念阐述清楚。有时为了方便，也可把最底下两层称为网络接口层。

![计算机网络体系结构]({{ site.baseurl }}/assets/images/computer-networks/computer-network-architecture.png)
*计算机网络体系结构*

#### （1）应用层（application layer）

应用层是体系结构中的最高层。应用层的任务是**通过应用进程间的交互来完成特定网络应用**。应用层协议定义的是**应用进程间通信和交互的规则**。这里的进程就是指主机中正在运行的程序。对于不同的网络应用需要有不同的应用层协议。在互联网中的应用层协议很多，如域名系统 DNS，支持万维网应用的 HTTP 协议，支持电子邮件的 SMTP 协议，等等。我们把应用层交互的数据单元称为**报文**（message）。

#### （2） 运输层（transport layer）

运输层的任务是**负责向两台主机中进程间的通信提供通用的数据传输服务**。应用进程利用该服务传送应用层报文。所谓“通用的”，是指并不针对某个特定网络应用，而是多种应用可以使用同一个运输层服务。由于一台主机可同时运行多个进程，因此运输层有复用和分用的功能。复用就是多个应用层进程可同时使用下面运输层的服务，分用和复用相反，是运输层把收到的信息分别交付上面应用层中的相应进程。运输层主要使用以下两种协议：

- **传输控制协议 TCP**（Transmission Control Protocol）：提供面向连接的、可靠的数据传输服务，其数据传输的单位是**报文段**（segment）。
- **用户数据报协议 UDP**（User Datagram Protocol）：提供无连接的、尽最大努力（best-effort）的数据传输服务（不保证数据传输的可靠性），其数据传输的单位是**用户数据报**。

#### （3） 网络层（network layer）

网络层负责为分组交换网上的不同**主机**提供通信服务。在发送数据时，网络层把运输层产生的报文段或用户数据报封装成**分组**或**包**进行传送。在 TCP/IP 体系中，由于网络层使用 IP 协议，因此分组也叫做 **IP 数据报**，或简称为**数据报**。

注意，不要将运输层的“用户数据报 UDP”和网络层的“IP 数据报”弄混。此外，无论在哪一层传送的数据单元，都可笼统地用“分组”来表示。

网络层的另一个任务是要选择合适的路由，使源主机运输层所传下来的分组，能够通过网络中的路由器找到目的主机。这里要强调指出，网络层中的“网络”二字，已不是我们通常谈到的具体网络，而是在计算机网络体系结构模型中的第 3 层的名称。

互联网是由大量的**异构**（heterogeneous）网络通过**路由器**（router）相互连接起来的。互联网使用的网络层协议是无连接的网际协议 IP（Internet Protocol）和许多种路由选择协议，因此互联网的网络层也叫做**网际层**或 **IP 层**。

#### （4）数据链路层（data link layer）

数据链路层常简称为**链路层**。我们知道，两台主机之间的数据传输，总是在一段段的链路上传送的，这就需要使用专门的链路层的协议。在两个相邻结点之间传送数据时，数据链路层将网络层交下来的 IP 数据报**组装成帧**（framing），在两个相邻结点间的链路上传送**帧**（frame）。每一帧包括数据和必要的**控制信息**（如同步信息、地址信息、差错控制等）。

在接收数据时，控制信息使接收端能够知道一个帧从哪个比特开始和到哪个比特结束。这样，数据链路层在收到一个帧后，就能从中提取出数据部分，上交给网络层。

控制信息还使接收端能够检测到所收到的帧中有无差错。如发现有差错，数据链路层就简单地**丢弃**这个出了差错的帧，以免继续在网络中传送下去白白浪费网络资源。如果需要改正数据在数据链路层传输时出现的差错（这就是说，数据链路层不仅要检错，而且要纠错），那么就要采用可靠传输协议来纠正出现的差错。这种方法会使数据链路层的协议复杂些。

#### （5）物理层（physical layer）

在物理层上所传输的数据的单位是**比特**。发送方发送 1（或 0）时，接收方应当收到 1 （或 0）而不是 0（或 1）。因此物理层要考虑用多大的电压代表“1”或“0”，以及接收方如何识别出发送方所发送的比特。物理层还要确定连接电缆的插头应当有多少根引脚以及各引脚应如何连接。当然，解释比特代表的意思，就不是物理层的任务。注意，传递信息所利用的一些物理媒体，如双绞线、同轴电缆、光缆、无线信道等，并不在物理层协议之内而是在物理层协议的下面。因此也有人把物理层下面的物理媒体当作第 0 层。

在互联网所使用的各种协议中，最重要和最著名的就是 TCP 和 IP 两个协议。现在人们经常提到的 TCP/IP 并不一定是单指 TCP 和 IP 这两个具体的协议，而往往是表示互联网所使用的整个 **TCP/IP 协议族**（protocol suite）。

![数据在各层之间的传递过程]({{ site.baseurl }}/assets/images/computer-networks/data-transition-between-layers.png)
*数据在各层之间的传递过程*

上图说明的是应用进程的数据在各层之间的传递过程中所经历的变化。这里为简单起见，假定两台主机通过一台路由器连接起来。

假定主机 1 的应用进程 $AP_1$，向主机 2 的应用进程 $AP_2$ 传送数据。$AP_1$ 先将其数据交给本主机的第 5 层（应用层）。第 5 层加上必要的控制信息 $H_5$ 就变成了下一层的数据单元。第 4 层（运输层）收到这个数据单元后，加上本层的控制信息 $H_4$，再交给第 3 层（网络层），成为第 3 层的数据单元。依此类推。不过到了第 2 层（数据链路层）后，控制信息被分成两部分，分别加到本层数据单元的首部（$H_2$）和尾部（$T_2$）；而第 1 层（物理层）由于是比特流的传送，所以不再加上控制信息。注意，传送比特流时应从首部开始传送。

OSI 参考模型把对等层次之间传送的数据单位称为该层的**协议数据单元 PDU**（Protocol Data Unit）。这个名词现已被许多非 OSI 标准采用。

当这一串的比特流离开主机 1 经网络的物理媒体传送到路由器时，就从路由器的第 1 层依次上升到第 3 层。每层都根据控制信息进行必要的操作，然后将控制信息剥去，将该层剩下的数据单元上交给更高的一层。当分组上升到了第 3 层时，就根据首部中的目的地址查找路由器中的转发表，找出转发分组的接口，然后往下传送到第 2 层，加上新的首部和尾部后，再到最下面的第 1 层，然后在物理媒体上把每一个比特发送出去。

当这一串的比特流离开路由器到达目的站主机 2 时，就从主机 2 的第 1 层按照上面讲过的方式，依次上升到第 5 层。最后，把应用进程 $AP_1$ 发送的数据交给目的站的应用进程 $AP_2$。

虽然应用进程数据要经过上图所示的复杂过程才能送到终点的应用进程，但这些复杂过程对用户来说，都被屏蔽掉了，以致应用进程 $AP_1$ 觉得好像是直接把数据交给了应用进程 $AP_2$。同理，任何两个同样的层次（例如在两个系统的第 4 层）之间，也好像如图中的水平虚线所示的那样，把数据（即数据单元加上控制信息）通过水平虚线直接传递给对方。这就是所谓的“**对等层**”（peer layers）之间的通信。我们以前经常提到的各层协议，实际上就是在各个对等层之间传递数据时的各项规定。

在文献中也还可以见到术语“**协议栈**”（protocol stack）。这是因为几个层次画在一起很像一个栈（stack）的结构。

### 4.3 实体、协议、服务和服务访问点

当研究开放系统中的信息交换时，往往使用**实体**（entity）这一较为抽象的名词表示**任何可发送或接收信息的硬件或软件进程**。在许多情况下，实体就是一个特定的软件模块。

**协议是控制两个对等实体（或多个实体）进行通信的规则的集合**。协议的语法方面的规则定义了所交换的信息的格式，而协议的语义方面的规则就定义了发送者或接收者所要完成的操作。**在协议的控制下，两个对等实体间的通信使得本层能够向上一层提供服务。要实现本层协议，还需要使用下一层所提供的服务**。

协议和服务在概念上是不同的。首先，协议的实现保证了能够向上一层提供服务。使用本层服务的实体只能看见服务而无法看见下面的协议。也就是说，下面的协议对上面的实体是透明的。其次，**协议是“水平的”**，即协议是控制对等实体之间通信的规则。但**服务是“垂直的”**，即服务是由下层向上层通过层间接口提供的。另外，并非在一个层内完成的全部功能都称为服务。只有那些能够被高一层实体“看得见”的功能才能称之为“服务”。上层使用下层所提供的服务必须通过与下层交换一些命令，这些命令在 OSI 中称为**服务原语**。

在同一系统中相邻两层的实体进行交互（即交换信息）的地方，通常称为**服务访问点 SAP**（Service Access Point）。服务访问点 SAP 是一个抽象的概念，它实际上就是一个逻辑接口，有点像邮政信箱（可以把邮件放入信箱和从信箱中取走邮件），但这种层间接口和两个设备之间的硬件接口（并行的或串行的）并不一样。OSI 把层与层之间交换数据的单位称为**服务数据单元 SDU**（Service Data Unit），它可以与 PDU 不一样。例如，可以是多个 SDU 合成为一个 PDU，也可以是一个 SDU 划分为几个 PDU。

这样，在任何相邻两层之间的关系可概括为下图所示的那样。这里要注意的是，第 n 层的两个“实体（n）”之间通过“协议（n）”进行通信，而第 n+1 层的两个“实体（n + 1）”之间则通过另外的“协议（n+1）”进行通信（每一层都使用不同的协议）。第 n 层向上面的第 n+1 层所提供的服务实际上已包括了在它以下各层所提供的服务。第 n 层的实体对第 n+1 层的实体就相当于一个服务提供者。在服务提供者的上一层的实体又称为“服务用户”，因为它使用下层服务提供者所提供的服务。

![相邻两层之间的关系]({{ site.baseurl }}/assets/images/computer-networks/relationship-between-2-layers.png)
*相邻两层之间的关系*

计算机网络的协议还有一个很重要的特点，就是协议必须把**所有**不利的条件事先都估计到，而**不能假定一切都是正常的和非常理想的**。例如，两个朋友在电话中约好，下午 3 时在某公园门口碰头，并且约定“不见不散”。这就是一个很不科学的协议，因为任何一方临时有急事来不了而又无法通知对方时（如对方的电话或手机都无法接通），则另一方按照协议就必须永远等待下去。因此，看一台计算机网络协议是否正确，不能只看在正常情况下是否正确，而且还必须非常仔细地检查这个协议能否应付各种异常情况。下面是一个有关网络协议的非常著名的例子。

【例 1-1】占据东、西两个山顶的蓝军 1 和蓝军 2 与驻扎在山谷的白军作战。其力量对比是：单独的蓝军 1 或蓝军 2 打不过白军，但蓝军 1 和蓝军 2 协同作战则可战胜白军。现蓝军 1 拟于次日正午向白军发起攻击。于是用计算机发送电文给蓝军 2。但通信线路很不好，电文出错或丢失的可能性较大（没有电话可使用）。因此要求收到电文的友军必须送回一个确认电文。但此确认电文也可能出错或丢失。试问能否设计出一种协议使得蓝军 1 和蓝军 2 能够实现协同作战因而一定（即 100% 而不是 99.999...%）取得胜利?

【解】蓝军 1 先发送：“拟于明日正午向白军发起攻击。请协同作战和确认。”假定蓝军 2 收到电文后发回了确认。然而现在蓝军 1 和蓝军 2 都不敢下决心进攻。因为，蓝军 2 不知道此确认电文对方是否正确地收到了。如未正确收到，则蓝军 1 必定不敢贸然进攻。在此情况下，自己单方面发起进攻就肯定要失败。因此，必须等待蓝军 1 发送“对确认的确认”。假定蓝军 2 收到了蓝军 1 发来的确认。但蓝军 1 同样关心自己发出的确认是否已被对方正确地收到。因此还要等待蓝军 2 的“对确认的确认的确认”。

这样无限循环下去，蓝军 1 和蓝军 2 都始终无法确定自己最后发出的电文对方是否已经收到（如下图所示）。因此，在本例题给出的条件下，没有一种协议可以使蓝军 1 和蓝军 2 能够 100% 地确保胜利。

![无限循环的协议]({{ site.baseurl }}/assets/images/computer-networks/recycle-protocol.png)
*无限循环的协议*

这个例子告诉我们，看似非常简单的协议，设计起来要考虑很多问题。

### 4.4 TCP/IP 的体系结构

前面已经说过，TCP/IP 的体系结构比较简单，它只有四层。下图给出了用这种四层协议表示方法的例子。注意，图中的路由器在转发分组时最高只用到网络层而没有使用运输层和应用层。

![TCP/IP四层协议的表示方法举例]({{ site.baseurl }}/assets/images/computer-networks/tcp-ip-protocol-example.png)
*TCP/IP四层协议的表示方法举例*

应当指出，技术的发展并不是遵循严格的 OSI 分层概念。实际上现在的互联网使用的 TCP/IP 体系结构有时已经演变成为下图所示的那样，即某些应用程序可以直接使用 IP 层，或甚至直接使用最下面的网络接口层，下图是这种表示方法。在图中，网络接口层有时也称为子网层。但本书不采用“子网层”这种容易弄混淆的表示方法，因为这里的“子网”是指一些局域网和某些广域网（如ATM 网），但从IP层来看，这些网络属于数据链路层，也就是属于网络接口层。此外，子网划分中的“子网”和下图中“子网层”中的“子网”是完全不同的概念。

![TCP/IP体系结构的另一种表示方法]({{ site.baseurl }}/assets/images/computer-networks/another-tcp-ip-protocol-example.png)
*TCP/IP体系结构的另一种表示方法*

还有一种方法，就是分层次画出具体的协议来表示 TCP/IP 协议族（下图），它的特点是上下两头大而中间小：应用层和网络接口层都有多种协议，而中间的 IP 层很小，上层的各种协议都向下汇聚到一个 IP 协议中。这种很像沙漏计时器形状的 TCP/IP 协议族表明：**TCP/IP 协议可以为各式各样的应用提供服务**（所谓的 everything over IP），同时 TCP/IP 协议也**允许 IP 协议在各式各样的网络构成的互联网上运行**（所谓的 IP over everything）。正因为如此，互联网才会发展到今天的这种全球规模。从图中不难看出IP协议在互联网中的核心作用。

![沙漏计时器形状的 TCP/IP 协议族示意]({{ site.baseurl }}/assets/images/computer-networks/tcp-ip-hourglass-timer-protocol.png)
*沙漏计时器形状的TCP/IP 协议族示意*

【例 1-2】利用协议栈的概念，说明在互联网中常用的客户-服务器工作方式。

【解】图 1-25 中的主机 `A` 和主机 `B` 都各有自己的协议栈。主机 `A` 中的应用进程（即客户进程）的位置在最高的应用层。这个客户进程向主机 `B` 应用层的服务器进程发出请求，请求建立连接（图中的 1）。然后，主机 `B` 中的服务器进程接受 `A` 的客户进程发来的请求（图中的 2）。所有这些通信，实际上都需要使用下面各层所提供的服务。但若仅仅考虑客户进程和服务器进程的交互，则可把它们之间的交互看成是如图中的水平虚线所示的那样。

![在应用层的客户进程和服务器进程的交互]({{ site.baseurl }}/assets/images/computer-networks/communication-between-client-and-server-in-application-layer.png)
*图 1-25 在应用层的客户进程和服务器进程的交互*

图 1-26 画出了三台主机的协议栈。主机 `C` 的应用层中同时有两个服务器进程在通信。服务器 1 在和主机 `A` 中的客户通信，而服务器 2 在和主机 `B` 中的客户 2 通信。有的服务器进程可以同时向几百个或更多的客户进程提供服务。

![主机C的两个服务器进程分别向A和B的客户进程提供服务]({{ site.baseurl }}/assets/images/computer-networks/server-side-communication-between-client-and-server-in-application-layer.png)
*图 1-26 主机 C 的两个服务器进程分别向A和B的客户进程提供服务*

## 五、参考

- 谢希仁，[计算机网络（第七版）](https://book.douban.com/subject/26960678/)