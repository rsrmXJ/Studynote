# TCP/IP 详解

### 第一章 概述

1\. 什么是 TCP/IP?

TCPP/IP 是一个 实现 Intemet 体系结构的协议族,它来源于 ARPANET 参考模型 (ARM) [RFCO871]。

- 协议族：一系列相关协议的集合

2\. TCP/IP 的起源？

**前言**

**什么是TCP？**

Transmission Control Protocol(传输控制协议)英文单词的缩写，负责在不同的传输信道上提供可靠的抽象层。

**什么是IP?**

IP，即Internet Protocol（因特网协议）英文单词的缩写，负责联网主机之间的路由选择和寻址。

**为什么会有 TCP/IP 协议？**

世界上，有各种各样的电脑运行着不同的操作系统为我们服务，但它们在表达同一种信息的时候使用的方法却是千差万别。于是计算机使用者意识到，计算机只是单兵作战并发挥不了太大的作用，只有将它们连接起来，电脑才会发挥出它最大的潜力。、

于是人们就想法设法用电线将电脑连接到了一起。

但简单的连接到一起是远远不够，就好像两个不同国家的人无法交流一样。因此他们需要定义一些共同的东西来进行交流，TCP/IP就是为此而生。TCP/TP不是一个协议，而是一个协议族的统称，里面包括了IP协议，IMCP协议，TCP协议，以及我们更加熟悉的http、ftp、 pop3协议等等。电脑有了这些，就好像学会了外语一样，就可以和其他的计算机终端做自由的交流了。 

**TCP/IP的起源**

TCP/IP起源于20世纪60年代末美国政府资助的一个分组交换网络研究项目，到90年代已发展成为计算机之间最常应用的组网形式。

它被称作“全球互联网”和“因特网”的基础。

**TCP/IP协议分层**

提到协议分层----我们很容易想到ISO-OSI的七层协议经典架构。即物理层、数据链路层、网络层、传输层、会话层、表示层、应用层。但TCP/IP协议则稍有不同。

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5Cf00119e472b8478db8500af3fbbd3ed3%5Cclipboard.png)

TCP/IP协议族按照层次由上到下，层层包装。

- 第一层就是应用层了，这里面有http，ftp,等等我们熟悉的协议。

负责处理特定的应用程序细节。

1. Telnet 远程登录
2. FTP 文件传输协议
3. SMTP 简单邮件传送协议
4. SNMP 简单网络管理协议

- 而第二层则是运输层，也称作是传输层。著名 的TCP（传输控制协议）和UDP(用户数据报协议)协议就在这个层次（不要告诉我你没用过udp玩星际）。

主要为主机上的应用程序提供端到端的通信。

TCP为两台主机提供高可靠的数据通信。它的工作包括把应用程序交给它的数据分成合适的小块交给下面的网络层，确认接收到的分组，

- 第三层是网络层，也称作是联网层，IP协议就在这里，它负责对数据加上IP地址和其他的数据（后面 会讲到）以确定传输的目标。（处理分组在网络中的活动，例如：分组的择路） 包括：IP协议（网际协议），ICMP协议（互联网控制报文协议）IGMP协议（internet组管理协议）

处理分组（或信息）在网络中的活动，例如：分组的选路，

UDP只是把数据包的分组从一台主机发送到另一台主机，但并不保证该数据报能到达另一端。

- 第四层是叫链路层，也称作数据链路层或网络接口层，这个层次为待传送的数据加入一个以太网协议头，并进行CRC编码，为最后的数据传输做准备。

通常包括操作系统中的设备动程序（以太网驱动程序）和计算机中对应的网络接口卡，他们一起处理与电缆（或其他任何传输媒介）的传输接口细节。

再往下则是 硬件层（物理层）次了，负责网络的传输，这个层次的定义包括网线的制式，网卡的定义等等（这些我们就不用关心了，我们也不做网卡），所以有些书并不把这个层次放在 tcp/ip协议族里面，因为它几乎和tcp/ip协议的编写者没有任何的关系。

发送协议的主机从上自下将数据按照协议封装，而接收数据的主机则按照协议 从得到的数据包解开，最后拿到需要的数据。这种结构非常有栈的味道，所以某些文章也把tcp/ip协议族称为tcp/ip协议栈。

网络协议通常分不同层次进行开发，每一层分别负责不同的通信功能。

TCP/IP同常被认为是一个四层协议系统。如下图所示：

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5C463ca82ee53242ddb44b868af27434d4%5Cclipboard.png)

1.3TCP和IP的分层

TCP和IP的相同点和不同点：

相同点：

1. 都使用IP协议
2. TCP/UDP的每组数据都通过端系统和中间的每个路由器中的IP层在互联网中进行传输。

不同点：

1. TCP提供一种可靠的传输层服务，但UDP是不可靠的，（因为它不能保证数据报不能安全无误的到达最终目的[接受端]）

ICMP是IP协议的附属协议。IP层用它来和其她主句或路由器交换错误报文或其他信息。

IGMP是internet组管理协议。它用来把一个UDP数据报多播到多个主机。

ARP（地址解析协议）和RARP（逆地址解析协议）是某些网络接口（如以太网和令牌环网）使用的特殊协议，用来转换IP层和网络接口层使用的地址。

1.4互联网的地址

互联网上的每个节点必须有一个唯一的internet地址（也称为IP地址）。IP地址长32位，并且IP地址由一定的结构如下图所示：

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5C9a1fe62d021742f3805baebb9aae57f0%5Cclipboard.png)

这些 32 位的地址通常写成四个十进制的数,其中每个整数对应一个字节。这种表示方法称作“点分十进制表示法(Dotted decimal notation)”。

1.5域名系统

为什么要有域名系统（DNS）？

尽管人们可以通过IP地址访问一台主机（识别网络接口，进而访问主机）但是人们还是喜欢使用主机名（域名）来访问。

域名系统能够是一个分部的数据库，它提供将主机名（就是URL）转换成IP的服务。

什么是RFC?

RFC就是TCP/IP的标准文档。

端口号

注意这个号码用在TCP,UDP上的一个逻辑号码，并不是一个硬件端口，我们平时说吧某某端口封掉了，只是在IP层次把这个带有这个号码的IP包过滤掉了。

应用编程接口

应用编程接口有socket和TLI。而前面的有时候也叫做“Berkeley socket”

1.6封装

从应用层传输数据的过程

当应用层程序需要传送数据时，数据被送入协议栈中(从顶向下)，然后逐个通过每一层直到被当作一串比特流入网络。其中每一层都要对接受到的数据增加一些首部（或尾部信息）。

TCP传给IP的数据单元称作TCP报文段或简称为TCP段（TCP segment）.

IP传送给网络层的数据单元称作IP数据报（IP datagram）.

通过以太网传输的比特流被称作帧（Frame）。

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5Ca021004711544879a5fc10318d26257f%5Cclipboard.png)

由于TCP、UDP、ICMP和IGMP都要向IP传送数据，因此IP必须在生成的IP首部中加入某种标识，以表明数据属于哪一层。为此，IP在首部中存入一个长度为8bit的数值，称作协议域。1表示为ICMP协议，2表示为IGMP协议，6表示为TCP协议，17表示为UDP协议。

IP和网络接口层之间传送的数据单元应该是分组（packet）。分组既可以是一个IP数据报，也可以是IP数据报的一个片（fragment）

**注意：**

**如何区分UDP和TCP的？**

TCP和UDP数据基本一致。唯一不同的是UDP传给IP的是UDP数据报（UDP datagram），而且UDP的首部长为8字节。

IP是如何区分TCP、UDP、ICMP和IGMP的？

由于TCP、UDP、ICMP和IGMP都要向IP传送数据，因此IP必须在生成的IP首部中加入某种标识，以表明数据属于哪一层。为此，IP在首部中存入一个长度为8bit的数值，称作协议域。1表示为ICMP协议，2表示为IGMP协议，6表示为TCP协议，17表示为UDP协议。

许多应用程序都使用TCP/UDP传输数据，那么该如何区分呢？

许多应用程序都可以使用TCP或UDP来传送数据。运输层协议在生成报文首部时要存入一个应用程序的标识符。TCP和UDP都用一个16bit的端口号来表示不同的应用程序。TCP和UDP把源端口号和目的端口号分别存入报文首部中。

网络层如何区分IP、ARP 和RARP协议？

网络接口分别要发送和接收IP、ARP和RARP数据，因此也必须在以太网的帧首部中加入某种形式的标识，以指明生成数据的网络层协议。为此，以太网的帧首部也有一个16 bit的帧类型域。

1.7分用

什么是分用？

当目的（接受端）主机收到一个以太网数据帧时，数据就开始从协议栈中由底向上升，同时去掉各层协议加上的报文首部。每层协议盒都要去检查报文首部中的协议标识，以确定接收数据的上层协议。这个过程称作分用（Demultiplexing）

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5C3a614f9f8cba4904bac3ad9cbe7bb319%5Cclipboard.png)

为协议ICMP和IGMP定位一直是一件很棘手的事情。在图1-4中，把它们与IP放在同一层上，那是因为事实上它们是IP的附属协议。但是在这里，我们又把它们放在IP层的上面，这是因为ICMP和IGMP报文都被封装在IP数据报中。

对于ARP和RARP，我们也遇到类似的难题。在这里把它们放在以太网设备驱动程序的上方，这是因为它们和IP数据报一样，都有各自的以太网数据帧类型。但在图2-4中，我们又把ARP作为以太网设备驱动程序的一部分，放在IP层的下面，其原因在逻辑上是合理的。

这些分层协议盒并不都是完美的。

1.8客户/服务器模型

这种服务（服务器为客户端提供一些特定服务）分为两种类型：重复型和并发型

重复型服务器通过以下步骤进行交互：

1. 等待一个客户请求的到来
2. 处理客户请求
3. 发送响应给发送请求的客户端
4. 返回第一步

重复型服务器的主要问题发生在第二步，这个时候，它是不能为其他客户即提供服务

并发型服务请通过以下步骤进项交互：

1. 等待一个客户请求的到来
2. 启动一个新的服务器来处理这个客户的请求。在这期间生成一个新的进程、任务或线程。并依赖顶层操作系统的支持。这个步骤如何进行取决于操作系统。生成的服务器对客户的所有请求进行处理。处理结束后，终止这个新服务器。
3. 返回第一步

并发服务器的优点：

利用生成的服务器方法来处理客户的请求。可以说每个客户端都有与它自己对应的服务器。如果操作系统允许，就可以同时为多个用户服务。

为什么要对服务器进行分类而不是对可短端进行分类？

因为对于一个客户端来说，它通常不能辨别是与重复型服务请对话还是并发型服务器对话。

**一般来说，**TCP服务器是并发的，而UDP服务器是重复的。

1.9端口号

这些端口号是如何选择的？

服务器一般都是通过知名端口号来识别的。

客户端口号有称作临时端口号（存在时间很短）。

各个端口号的范围：

临时端口号 1024~5000

临时端口号

1.10标准化过程

有ISOC IAB IETF IRIF

1.12标准的简单服务：

当使用TCP和UDP提供相同的服务时，一般选择相同的端口号。

1.13互联网

**互联网由以太网和令牌环网两个网络组成。**

**internet和Internetd 区别？**

**internet意思是用一个共同的协议族把多个网络连接到一起。而Internet是指所用通过TCP/IP互联通信的所有主机结合。Internet是一个internet,但internet不等于Internet**

**1.14实现**

标准TCP/IP实现来自于位于伯克利的加利福尼亚大学的计算机系统研究小组。从历史上看，软件是随同4.x BSD系统（Berkeley Software Distribution）的网络版一起发布的。它的源代码是许多其他实现的基础。

**1.15应用编程接口**

socket和TLI（运输层接口：Transport Layer Interface）。前者有时称作“Berkeley socket”，表明它是从伯克利版发展而来的。后者起初是由AT&T开发的，有时称作XTI（X/Open运输层接口），以承认X/Open这个自己定义标准的国际计算机生产商所做的工作。XTI实际上是TLI的一个超集。

**1.16测试网络**

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5Cf671744e5b56460783ae682350820a98%5Cclipboard.png)

在TCP/IP中网络层和运输层最主要的区别：网络层（IP）提供点到点的服务，而传输层（TCP）提供端到端的服务。

一个互联网是网络的网络。构造互联网的共同即使是

第二章链路层

2.1引言

在TCP/IP协议族中，链路层主要有三个目的：

1. 为IP模块发送和接受数据报
2. 为ARP模块发送ARP请求和接受ARP应答
3. 为RARP发送RARP请求和RARP应答。

2.2以太网和IEEE 802封装

以太网一般是指数字设备公司（Digital Equipment crop）,因特尔公司（Intel Crop）和Xerox公司在1982年联合公布的一个标准。这个标准里使用一种称作CSMD/CD的接入方法。

数据链路层：以太网、坏牌令网，FDDI、PPP协议（adsl宽带），以及一个loopback协议

2.9路径MTU

2.10串行线路吞吐量计算

eg:如果线路速率是900b/s,一个字节有8bit,加上一个起始比特和一个停止比特，那么线路的速率就为960B/S（960字节/秒）

IP协议、ARP协议

IP是TCP/IP中最核心的协议。因为所有的TCP、UDP、IGMP、ICMP数据都以IP数据格式传输。

不可靠（unreliable）的意思是它不能保证IP数据包能成功的到达目的地。

无连接（connecttionless）每个数据包的处理是相互独立的。

3.2IP首部

普通的IP首部长为20字节，除非包含选项字段。

![img](C:%5CUsers%5CXJ%5CAppData%5CLocal%5CYNote%5Cdata%5CqqE41B863F36EED14D76C5682B570D0A8E%5Ca50f0ffa94e2402a9e301525b300f2df%5Cclipboard.png)