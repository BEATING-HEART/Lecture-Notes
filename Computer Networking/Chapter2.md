# 应用层原理

**总述**：应用层协议最多。中国在网络中应用层上的创新还不错，原创性创新一般。

**网络应用体系结构**：C-S模式、P2P模式以及混合型模式

> CS模式中，服务器是中心，先启动，并且服务器一般是固定IP。客户端主动找服务端请求资源。可扩展性比较差，随用户规模增加，系统性能断崖式下降。
>
> P2P模式中，可以做到平滑扩展。同一台主机，在某些会话中是服务器，在某些会话中又是客户端。
>
> 混合型架构：Napster，在校园网中分发mp3音乐。服务器作为目录和注册中心。做成了P2P文件共享系统。

**进程通信**：

同一主机的进程间，通过操作系统提供的进程通信机制进行通信。

不同主机，通过报文交换来通信。分为客户端进程（发起通信的进程）和服务端进程（等待连接的进程）

注意：P2P架构的应用也有客户端进程和服务端进程之分。

**分布式进程通信**：

> **应用进程如何标识自己，如何让别人找到自己？**标识和寻址。有三个要素，主机IP，协议TCP/UDP，端口号。例如HTTP采用TCP协议，在80端口。一个进程采用IP+port标识端节点。本质上，任何一对应用进程之间的通信就是两个端节点（end point）之间的通信
>
> **应用层如何使用传输层提供的服务？**形式：使用应用程序接口API也就是socket API，位置：层间接口的SAP处，就是socket。
>
> 层间接口必须携带的信息：
>
> 1. 要传输的报文（即传输层SDU）
> 2. 然后是源主机以及目的主机的标识（ip+port）。
>
> 传输时，可能在某个时间段内，要传输大量的信息，如果每个报文都要加上ip和port，那么会造成很大浪费。可以采用socket，减少层间传输的信息量。
>
> socket：用代号（一个整数），表示通信的双方或单方。用一个整数表示两个应用实体之间的通信关系，代表自己和对方的ip和port的**本地表示**。可以使得穿过层间接口的信息量最小，且便于管理。
>
> socket是本地应用层和传输层的约定，只有本地操作系统知道，对方是不知道的。收发的时候**查表**就行，操作系统负责维护这张表
>
> TCP的socket，代表的是**会话关系**，是**四元组，源和目标的ip+port**。目标主机两个不同的端口，对应两个不同的socket
>
> UDP的socket，对应**二元组，包含本地的ip+port**。通过层间接口时：需要传数据、传UDP socket和目标主机的ip+port才能发送出去。
>
> **应用进程使用传输层服务，如何实现报文交换？**定义协议，编写程序，借助OS的接口

**如何描述传输层的服务？应用层需要传输层提供什么样的服务？**

* 数据丢失率
* 吞吐量
* 时间敏感性（延迟）
* 安全性（机密性、完整性、可认证性）

TCP**实体**：实现网络协议（TCP协议）的软硬件模块

**传输层提供的服务**

* TCP服务：可靠、流量控制、拥塞控制、面向连接。不保证时间、最小吞吐和安全性。
* UDP服务：不可靠，不控制流量、拥塞，没有时间、带宽的保证。不建立连接。

**安全性**：

* TCP/UDP都没有加密，明文传输
* SSL是在TCP上实现的。提供加密的TCP连接（私密性、数据完整性、可以提供端到端鉴别）
* SSL在应用层，采用SSL库，SSL库采用TCP通信
* SSL socket API

# WEB与HTTP

**web页**：由一些对象组成（可以使HTML文件，可以使JPG图片，Java程序等）

web页含有一个基本的HTML文件，这个文件里又包含对于若干个对象的引用。通过url对每个对象进行引用。

url的格式为```Prot://user:psw@www.someSchool.edu/someDept/pic.gif:port```（协议名、用户、口令、主机名、路径名、端口）

**HTTP**：超文本（携带各种资源）传输协议，是web的应用层协议，采用**客户（浏览器）/服务器**模式。跑在TCP上。是无状态的。

服务器在80号端口打开一个socket（守候socket），然后当一个web浏览器与服务器建立连接时，新建另一个socket（连接socket）来标识这个连接。原来的守候socket仍然在80端口监听

* 非持久HTTP（HTTP 1.0）：TCP连接建立（一趟往返），HTTP传数据（一趟往返），TCP连接拆除。
* 持久HTTP（HTTP 1.1）：TCP连接建立，HTTP连接，TCP连接不关，可以一直通过HTTP 传数据。

如果使用HTTP 1.0，我们通过解析base.html文件，发现了（如10个）资源，然后要再次建立TCP连接，请求对象，然后拆除关闭连接。

**相应时间模型**

* **RTT(round-trip time)** 往返时间
* HTTP 1.0（非持久HTTP）的响应时间是：2RTT+传输时间
* HTTP 1.1（持久HTTP）
  * 流水线方式：请求直接 ”流水“ 发出
  * 非流水线方式：一个一个请求。前一个对象回来，后一个请求发出

**HTTP请求报文**：

* 采用ASCII码（人能阅读）
* 请求行包括method（GET/POST/HEAD/PUT/DELETE），url和version信息。
  * put和delete通常是维护使用。需要网络管理员使用一些工具进行请求。
  * head命令通常只请求首部，用于搜索引擎建立索引或者网站维护。
* 首部行（好多行，格式为首部名：首部值）
* 换行回车符，表示报文结束
* 如果有body那再传body（有些命令如post包含body，有些命令不包含body）

TCP上运行的所有应用进程，都**需要自己维护报文之间的界限**。

**HTTP响应**：

状态码：200 OK，301 已被永久移除， 400 请求无法理解， 404 资源未找到， 505 版本号不支持。

**HTTP协议是无状态的协议**（服务器不维护客户端的状态）

**Cookie机制维持客户状态**：第一次发送请求，不带cookie，服务器生成并发送cookie，后续请求携带cookie。可以做用户行为的分析，但是也涉及到隐私问题。

**web缓存机制**：用户通过代理服务器访问资源。代理服务器通常较多。代理服务器没有，则向original server请求，并缓存，然后返回给用户。（好处：用户体验快。服务器端的负载较轻。）

为什么要用web缓存？大家的访问具有趋同性。如果流量强度大，那么排队延时就会很大。使用缓存可以减少网络的载荷。 

缓存：本地命中，直接返回，本地不命中，从外网服务器中取数据。如果original server的对象发生修改，检查web缓存，有需要则更新 

# FTP协议

 现在用的已经不多。

**功能**：可以上传下载（对于客户端而言）。

**工作原理**：**控制连接与数据连接分开**。FTP服务器守护在21端口。先建立TCP连接（控制连接），然后进行身份认证（用户名和口令），完成认证后，客户端可以通过某种指令，向服务器请求某个文件。服务器收到指令，会**主动（有点违反常理）**与客户端20号端口建立TCP数据连接。

> 控制报文与数据报文分开传，就是**带外的**。合在一起就是**带内的**。

**组成**：FTP客户端，FTP服务器，用户接口，本地文件系统。

FTP是有状态的。要维护客户端的状态。

**FTP命令与响应**：

* **命令**在控制连接上以ASCII码文本的方式传送。如USER username，PASS password，LIST，RETR filename，STOR filename等
* **返回码**样例：状态码和状态码解释。

# E-Mail电子邮件

**组成部分**：用户代理（客户端软件outlook，foxmail等），邮件服务器（就是服务器），发送协议：简单邮件传输协议SMTP，拉取协议：POP 3，IMAP，HTTP也可以。

> IMAP协议不仅可以拉取，还可以远程管理目录，比如在收件箱中建文件夹，移动邮件到另一个文件夹中等。但POP 3 协议不支持这个功能。

**发信（SMTP协议）**：用户代理，配置好邮件服务器的ip地址端口号，通过邮件服务器发邮件。用户代理把邮件发给邮件服务器，暂存在队列中，邮件服务器从队列中挨个取邮件，然后向相应的目标邮件服务器发送。目标邮件服务器受到邮件后，放到每个用户的mailbox中。

**收信（三种协议都可）**：用户通过用户代理，从邮件服务器中的邮箱里拉取收到的信件。

**为什么放在队列中发？**进来的速度和处理（发出）的速度有差别，要用队列平滑一下。发邮件不是来了就发，而是定期发，比如五分钟十分钟。不至于服务器那么劳累。有时候也没有这么多邮件发，所以是定期发。

**email过程，简单SMTP发送**：简单，采用ascii码。容易被篡改。安全性不高。可打印，所以是readable。

> SMTP和HTTP的比较。都采用ASCII码，都支持相应交互。
>
> HTTP是pull的，每个对象有单独的相应报文。
>
> SMTP是push的，多个对象整合在一个报文里（邮件可携带附件）。

**SMTP协议包含**：首部行（包括to，from，subject还有抄送人cc等），空行以及body

**但这里会产生一个问题，原式的SMTP只支持ASCII，那么图片，汉子等都传不了了。所以有MIME多媒体邮件扩展。**

MIME采用了base64的编码。base64编码是通过ASCII码做的映射。相当于把若干个不在ASCII码范围内的字节，转化为在ASCII范围内的字节，然后进行传输。

因此原式的邮件协议经过扩展，可以传输中文和exe等文件。

**POP 3协议**：

可以跑在tcp上也可以跑在ssl上。

POP 3可以采用下载并删除模式（一个端看了另一个端就看不到了）和下载并保留模式（每个端都可以看到）。

POP 3是无状态的协议。

**IMAP协议**：有状态，因为可以维护远程目录

# DNS (Domain Name System) 域名解析系统

不是给人使用的。而是为其他应用所使用的。提供域名到ip地址的转换。是跑在应用层的基础设施。

web系统以及ftp等系统都要使用DNS服务。

**DNS系统的必要性**

网络层工作的主机，通过ip地址标识自己，也方便别人找到它（寻址）。但ip地址不好记。人们常常习惯于用有意义的字符串来标识internet上的设备。因此，就需要一个DNS系统，通过字符串找到ip。

**如何命名？**分层的命名，方便增加。

**对应关系如何解析？**不能集中式维护。分布式的数据库，进行解析。

**运行在UDP的53号端口。**

DNS是在主机系统的应用层实现的。是互联网的核心功能，但不在网络核心，而在边缘系统实现。**互联网的复杂性体现在网络边缘。在边缘的传输层以上。**

**DNS的主要目的**：

提供域名到ip地址的转换，提供主机别名到规范名字的转换。还提供负载均衡的功能。

> 一个域名通常对应一个服务器集群去支持它的访问。比如github.com这个域名，必定对应着许多服务器。github.com这个域名是所有服务器的别名。每个服务器为了区分，有着不同的规范名字，如server123.github.com等。

**DNS采用层次树状结构的命名方法。**树叶代表主机。

顶级域

> 通用：.com  .net  .gov  .edu  .org等
>
> 国家：.cn  .us  .nl  .jp等

每个域（子域）下面可以更加分为不同的子域。在子域可以对每台主机进行命名。**解决了重名问题**：从本域往上，用点号分割。

域是逻辑的。与物理网络无关。

> 一个域的主机，可以分属不同的网络；在同一个网络的主机，可能分属不同的域。

**DNS根名字服务器**

所有的域名归结到一个根上。但如果只有一个名字服务器的话，如果宕机就会有灾难性后果（可靠性问题），通信容量也不够大（可扩展性问题），维护非常麻烦（可维护性问题）。互联网的根名字服务器一共有13个。

查询域名的时候，要从13个根的任何一个往下去寻找。这样比较可靠一点。这13个根大部分在北美，欧洲和日本也有，中国大陆没有。

**DNS13个根一个都不在中国，有安全风险？**是又不是。

为了解决单个名字服务器的可靠性，可扩展性以及可维护性的问题，**我们将域分成一个个区域**，区域互不重叠。

每一个区域设置一个**权威名字服务器**。维护这个区域上的域名和ip地址。名字服务器可以设置在区域之外，以保障可靠性。

**TLD服务器**：顶级域服务器负责查询顶级域名和国家域名。

**区域名字服务器维护资源记录（数据库）**：域名到ip地址的对应关系，子域等信息。字段包含域名、ttl生存时间、class，value和type。

> ttl生存时间是为了效率而存在的，ttl到期则删除，保证一致性（如果是权威记录，那么ttl是无限大）
>
> class是代表网络类型。如IN代表Internet互联网。
>
> type表示不同的意思
>
> * type是A的话：name字段代表主机，value字段代表IP地址
> * type是CNAME的话：name字段为规范名字的别名，value字段放规范名字
> * type是MX Mailbox的话：name字段是邮件服务器的别名，value放的是邮件服务器别名所对应的规范名字
> * type是NS，那么name是域名，value为该域名的权威服务器域名。

**DNS大致工作流程**

1. 应用调用解析器resolver

2. 解析器作为客户，向Name Server发出查询报文（UDP）

   > 解析器怎么知道local name server的ip地址？是配置好的。或手工配，或者通过DHCP协议自动配。
   >
   > 一个机器上线之后，必须要知道这几个信息：ip地址，子网掩码，local name server是什么，default gateway默认网关
   >
   > **本地名字服务器**LNS并不严格属于层次结构，每个ISP（居民区的ISP，公司，大学）都有一个本地的DNS服务器。也称为默认名字服务器。当一个主机发起DNS查询时，这个查询就被发送到其本地DNS服务器上（起着代理的作用，将查询转发到层次结构中去）
   >
   > 如果LNS中有缓存，直接返回。
   >
   > 如果没有缓存
   >
   > * 采用递归查询方法，顺着树根查下来（往下找，一直找到权威的名字服务器，然后得到域名和ip的关系）但是这种递归查询很烦，因为都要问根服务器，所以就很忙。
   > * 还有迭代查询方法，“类似踢皮球”

3. Name Server返回相应报文（name / ip）

**DNS协议、报文**

查询和相应的报文相同。

报文首部：标识符(ID)，flags（包括：查询/应答，希望递归，递归可用，应答为权威等字段）

**提高性能**：缓存

一旦名字服务器学到了一个映射，就将该映射缓存起来。

根服务器通常都在本地服务器中缓存着，使得根服务器不用经常被访问。

可能存在的问题：如果情况发生变化，缓存结果就会和权威记录不一致。所以设置TTL（默认两天）

**维护问题：新增一个域**

上级域的名字服务器中增加两条记录，指向这个新增的子域的域名和域名服务器的地址。

在新增子域的名字服务器上运行名字服务器，负责本域的名字解析。名字->IP地址。

**攻击DNS**： 总的来说DNS比较健壮。

**DDOS攻击**

* 对服务器进行流量轰炸攻击：发送大量ping命令。

  > 没有成功，原因是：根目录服务器配置了流量过滤器，防火墙。local DNS服务器缓存了TLD服务器的IP地址，因此无需查询根服务器。

* 向TLD服务器进行流量轰炸攻击：发送大量查询

  > 可能更危险，但是效果一般。因为大部分DNS缓存了TLD。

**重定向攻击**

* 中间人攻击

  > 截获查询，伪造回答，从而攻击某个（DNS回答指定的IP）结点

* DNS中毒

  > 发送伪造的应答给DNS服务器，希望它能够缓存这个虚假的结果。

* 技术上较困难

**利用DNS基础设施进行DDOS攻击**

* 伪造某个IP进行查询，攻击这个目标IP。
* 查询放大，相应报文比查询报文大
* 效果有限

**因为基本上所有DNS服务器都缓存了顶级域名的对应关系，所以即便中国没有13根，也没有那么大的安全隐患。**

# P2P应用

**纯P2P应用**：没有（或极少）一直在运行的服务器。任意端系统都可以直接通信。P2P系统既是客户端，又是服务器。peer结点间歇性上网，每次IP地址都有可能变化。

可靠性高。流量是分布式的。是由成千上万个peer结点提供的。

**例子**：文件分发（BitTorrent），流媒体（KanKan），VoIP（Skype互联网电话）等

**传统的CS架构分发文件**：从服务器向N台不同的主机，分发统一文件F。时间下限为：$$\max\{\frac{F}{D_{min}},\frac{NF}{U_s}\}$$（$$D_i$$代表客户端下载带宽，$$U_s$$是服务器的上传带宽）**随着客户端数N越来越大，服务器的上载带宽成为瓶颈，下载时间随着用户增加线性增加。**

* 当客户端少时，瓶颈是客户端的下载带宽。
* 当客户端多时，瓶颈是服务端的上载带宽。

**P2P分发文件**：为了解决CS模式可扩展性问题。分发文件F的时间下限为$$\max\{\frac{F}{D_{min}},\frac{NF}{U_s},\frac{NF}{U_s+\sum_{i=1}^N U_i}\}$$（假设服务器有一个文件的拷贝，每一个客户端都有一份文件，每一个节点都可以上载。）但是由于P2P网络具有动态性，因此不易管理。

**P2P的类型**

* 非结构化P2P
* DHT（结构化）P2P 【基于分布式散列表】（暂不学

**P2P应用通过会话间的合作关系，相互协作，形成了一个覆盖网overlay（是一种应用层上的逻辑网络）**如果overlay是任意的、随机的，就是非结构化的P2P；如果overlay是有序的，构成环或者树，那就是DHT结构化的。

**非结构化P2P**

* 有集中化目录的文件分发系统（如Napster）

  * 每个结点上线时都要向目录服务器注册（我是哪台主机，我有哪些资源）
  * 结点请求资源时，要从目录服务器获得资源结点list（哪些结点有这个资源）然后向这些结点请求资源。
  * 目录服务器存在单点故障、性能瓶颈的问题。也可能存在侵权问题。

* 完全分布式的文件分发系统（如Gnutella）

  * 没有中心服务器。所有结点构建成一个Overlay。

    > 如何建立Overlay？下载Gnutella客户端时，有一个配置文件。其中有若干个Gnutella网络经常在线的结点的IP。
    >
    > 只要同其中一个建立联系，那么就可以通过其中转，随机与一些结点连接，接入Gnutella网络。
    >
    > 如果要下线，那么要一个个与邻居切断联系。此时邻居需要随机找另一个主机连接，以维持连接强度。

  * 结点通过泛洪的方式进行资源查询。通过Overlay，先找邻居，然后再找邻居的邻居，以“发洪水”的方式查询全网。拥有资源的服务器给出应答。以此构建资源目录。

    > 泛洪查询方法存在问题。查询可能会陷入循环，还可能被放大。这样不行
    >
    > 解决方法：1. 设置TTL，比如说五跳查询失效；2. 添加一个结点访问状态。被访问的结点不在泛洪。

  * 根据资源目录，向拥有资源的服务器发起查询

  > Gnutella的运行非常不成功。有人说在Gnutella网络上什么也找不到。后来才被迫开源，但也没有扩充太多用户群。

* 混合体，利用不对称性（例如KaZaA）

  * 每个结点都属于一个组。要么是组长，要么是组员。
  * 组长和组长的关系是Gnutella关系。组长和组员的关系是Napster关系。**组内集中式，组间分布式**
  * 将文件对应的哈希值，作为唯一ID，标识文件。

**BitTorrent（也属于非结构化）**

**工作原理**：peer要加入一个Torrent中（Torrent：结点的组，之间互相交换文件，互通有无）

每个结点都拥有文件的一部分块。根据拥有块的多少，有一个对应的bitmap。每隔一段时间，torrent中的所有结点会通过泛洪的方式，交换bitmap。

一个新结点加入时，bitmap全0，随机请求4块。然后根据稀缺优先原则，请求剩下的块。同时，按照**tit-for-tat（针锋相对）**的策略为其他结点提供服务。（对我好的节点，我也对他好）

> 由于稀缺优先的策略，所以自己被别人请求的概率就会变大，所以别人就会给自己提供更好的服务。所以，根据针锋相对的规则，自己的下载速度就会更快。性能就会更高
>
> 如果一个结点拥有这个文件的全部，就是**种子**。否则就是**吸血鬼**。种子可以选择离开。

是不是所有结点来了都要提供服务呢？采取**有限疏通**的方式。每三个周期中，前两个周期，排队优先级根据对方提供的带宽大小确定。第三个周期，采用随机的方法优化疏通。

**如何加入torrent：**

* 采用带外方式。在文件分发的网站，根据描述进行匹配，找到对应的hash值，然后下载对应的torrent文件。
* torrent文件是描述信息，携带了维护这个文件的tracking server（跟踪服务器）信息
* 检索的这个结点，就要向tracking server发送加入洪流的请求。通过tracking server找到peer结点，构成洪流，互通有无

**结构化DHT P2P**：维护的是一种树状、环状的有序拓扑。不是用IP地址，而是用IP地址的hash值作为唯一标识。

以环状拓扑为例，结点按照hash值大小首尾相连构成环。然后对文件也作哈希。

比如说一个DHT P2P中有【5】和【88】等结点，那么文件哈希值为6-88的，那么这些文件的目录，都维护在88这个结点处。

# 视频优化服务和CDN：上下文

互联网中，杀手级应用有很多种，其中视频业务是其中一种。**据统计，互联网中七八成的流量都被视频流量占据。**

**杀手级应用**：网络流量占的比较多，而且可以吸引用户。

**如何向成千上万的人提供并行的播放业务？**挑战：规模性问题，以及每个结点的网络带宽制约，需求不同。由于客户端异构，需要的视频质量也不尽相同

**采用分布式的应用层面的基础设施CDN来解决**。

**视频是什么**？图像序列。利用了视觉的滞留效应。有一个帧率。

**视频压缩**：视频如果不压缩，直接传输，通常占用带宽很大。一般都**以小码率传输**。对时间冗余和空间冗余进行压缩。

有CBR和VBR两种编码策略。

**存储视频的流化服务**一边下一遍播放。

**多媒体流化服务器DASH**

* 服务器将文件分成多个块，以不同码率编码、存储。
* 生成告示文件manifest，提供不同块的URL。
* 边看便请求，自动决定要向哪些服务器请求后续块。

**Content Distribution Network (CDN) 内容分化网络**：服务器如何通过网路向上百万用户同时提供流化视频服务？

**存在的问题**：1.跳数比较多  2.网上的重复流量比较多（浪费）3. 单点故障 4.服务器瓶颈 5. 网络拥塞

**CDN服务商在全球部署CDN缓存结点**。用户可以通过域名解析重定向的技术，定位到（离他最近的，服务最好的）CDN服务器。

视频流媒体提供者，购买CDN运营商的服务。ISP购买CDN运营商的服务，提升ISP运营商服务用户的能力。

**CDN运营商的部署策略**：

* **enter deep 策略**：部署在local ISP，深入接入网，离用户进，数量多，质量好，但难以管理。
* **bring home 策略**：部署在少数关键结点。而是部署到上层的ISP的附近，如在某些数据中心机房附近。

**用户怎么知道从哪个CDN访问资源呢**：1. manifest文件 2. 域名解析重定向 3. 如果网络路径堵塞，可以选择不同的拷贝

**CDN结点运行在应用层，运行在网络的边缘，呈现OVER-THE-TOP的特点。挑战主要在**：

* 那么多节点有，我从哪个缓存节点获取？
* 如果结点堵塞，我怎么换另一个结点？
* 我应该在那些结点部署拷贝？

**CDN使用的简单场景**：

1. 用户通过视频链接，访问并解析。
2. 然后根据DNS解析，问LocalNameServer。
3. 从LNS的权威名字服务器中，查到的不是一个ip，而是重定向（返回了另一个域名）
4. 再去解析返回的命名，就会解析到CDN的权威名字服务器。
5. 然后CDN的权威名字服务器，给到一个ip地址，这个IP地址是离用户最近的。
6. 由这个IP地址向用户提供流化服务。

CDN的重定向对用户是透明的，根本感受不到是原始服务器还是CDN服务器提供服务。

# TCP Socket编程

应用进程使用传输层提供的服务，才能互相通信。传输层提供Socket API

* TCP 可靠的，字节流的服务，无连接。**保证按照流是对的，但是不保证界限。报文需要应用自己维护**
* UDP 不可靠的（数据UDP数据报）数据报，有连接。

**前面学过，Socket 就是一个整数**。就像文件句柄一样。对Socket操作，就是对会话上两个应用进程之间的操作。TCP Socket的整数代表了自己的ip+port和对方的ip+port，代表两个进程间的会话关系。

**TCP Socket编程场景**：**通过socket的方式，将小写字母变成大写**。

1. 前提：服务器要先运行（C/S模式）等待连接
2. 服务器创建welcome Socket，返回一个整数，然后这个socket会与服务器本地的ip/port相捆绑。
3. 服务器调用socket api中的accept函数。阻塞式等待远程客户端的socket连接建立请求。
4. 之后，客户端也要创建socket，然后返回整数，与客户端本地的ip/port进行隐式（如果你不指定，就默认和当前一个没有用的端口）的捆绑。
5. 客户端调用calnet函数，指定服务器的ip/port，与服务器进行阻塞式连接。
6. 如果客户端收到了服务器的确认，那么连接建立。此时客户端calnet就会解除阻塞，向客户端返回一个有效值。
7. 服务端的welcome socket在accept客户端的请求后，会返回一个新的socket（connection socket）包含了服务器ip/port（没变），还包含了客户端的ip/port。
8. 连接建立，然后可以开始收发。收发完成后，解除connection socket，但welcome socket仍然在守候。

![image-20220307104911476](Chapter2.assets/image-20220307104911476.png)

**两个重要的结构体**

```c
struct sockaddr_in {
	short sin_family; //AF_INET 协议族
	u_short sin_port; //port
	struct in_addr sin_addr; // ip address, unsigned long
	char sin_zero[8]; //align
}
```

不仅可以用于IP通信，也可以用IPX的通信。

```c
struct hostent {
	char *h_name; // 主机域名
	char **h_aliases; // 主机别名
	int h_addrtype;
	int h_length; //地址长度
	char **h_addr_list; // 给出ip地址列表
	#define h_addr h_addr_list[0];
} // 主要作域名解析的函数调用
```

**客户端TCP socket代码**

```c
/* client.c */
void main(int argc,char *argv[]){
    struct sockaddr_in sad; /* structure to hold an lP address of server */
    int clientSocket; /* socket descriptor */
    struct hostent *ptrh; /* pointer to a host table entry */
    char Sentence[128];
    char modifiedSentence[128];
    host = argv[1]; port = atoi(argv[2]);
    
    /* Create client socket, connect to server: */
    memset((char *)&sad,0,sizeof(sad)); /* clear sockaddr structure */
    sad.sin_family = AF_INET; /* set family to Internet */
    sad.sin_port = htons((u_short)port);
    ptrh = gethostbyname(host); /*Convert host name to lPaddress */
    memcpy(&sad.sin_addr, ptrh->h_addr， ptrh->h_length); //将lP地址拷贝到sad.sin__addr
    connect(clientSocket, (struct sockaddr *)&sad, sizeof(sad));
    
    /* get input stream from user*/
    gets(Sentence);
    
    /*send line to server*/
    n = write(clientSocket, Sentence, strlen(Sentence)+1);
    
    /* Read line from server */
    n=read(clientSocket, modifiedSentence, sizeof(modifiedSentence));
    
    printf("FROM SERVER: %sln",modifiedSentence);
    
    /*Close connection*/
    close(clientSocket);
}
```

**服务端TCP socket代码**

```c
/*server.c */
void main(int argc, char *argv[]){
    struct sockaddr_in sad; /* structure to hold an lP address of server*/
    struct sockaddr_in cad; /*client */
    int welcomeSocket,connectionSocket; /* socket descriptor *lstruct hostent *ptrh; l* pointer to a host table entry */
    char clientSentence[128];
    char capitalizdSentence[128];
    port = atoi(argv[1]);
    
    /* create welcoming socket at port & bind a local address*/
    welcomeSocket = socket(PF_INET,SOCK_STREAM，O);
    memset((char *)&sad,0,sizeof(sad)); /* clear sockaddr structure *lsad.sin_family = AF_INET; /* set family to lnternet */
    sad.sin_addr.s_addr = INADDR_ANY; /*set the local lP address */
    sad.sin_port = htons((u_short)port);/*set the port number */
    bind(welcomeSocket, (struct sockaddr *)&sad, sizeof(sad));
    
    /* Specify the maximum number of clients that can be queued */
    listen(welcomesocket，10);
    while(1) {
        /*wait on welcoming socket for contact by a client*/
    	connectionSocket=accept(welcomeSocket,(struct sockaddr *)&cad,&alen);
        n=read(connectionSocket, clientSentence, sizeof(clientSentence));
        /*capitalize Sentence and store the result in capitalizedSentence*/
        /*write out the result to socket*/
        n=write(connectionSocket, capitalizedSentence, strlen(capitalizedSentence)+1);
        close(connectionSocket);
    } // end of while loop, loop back and wait for another client connection.
}
```

# UDP Socket编程

**没有握手**。socket只和本地ip/port捆绑，作为本地ip/port的代表。

所以**发送报文时**以socket作为参数，必须明确指明对方的ip/port

进程**接收报文时**，必须从报文中提取出发送者的ip/port这样才能发回去。

可能乱序，可能丢失。因为无连接，所以可以叫做数据报。

![image-20220308000145832](Chapter2.assets/image-20220308000145832.png)

**UDP客户端代码**

```c
/* client.c */
void main(int argc，char *argv[]){
    struct sockaddr_in sad; /* structure to hold an lP address */
    int clientSocket; /* socket descriptor */
    struct hostent *ptrh; /* pointer to a host table entry */
    char Sentence[128];
    char modifiedSentence[128];
    host = argv[1]; port = atoi(argv[2]);
    clientSocket = socket(PF_INET,SOCK_DGRAM,0);/* determine the server's address */
    memset((char *)&sad,0,sizeof(sad)); /* clear sockaddr structure */
    sad.sin_family = AF_INET;/* set family to Internet */
    sad.sin_port = htons((u_short)port);
    ptrh = gethostbyname(host);
    /* Convert host name to lPaddress */
    memcpy(&sad.sin_addr,ptrh->h_addr， ptrh->h_length);
    gets(Sentence); /*Get input stream from user*/
    /* send line to server */
    addr_len =sizeof(struct sockaddr);
    n=sendto(clientSocket, Sentence,strlen(Sentence)+1,(struct sockaddr *) &sad， addr len);
    /*read line from server*/
    n=recvfrom(clientSocket, modifiedSentence,sizeof(modifiedSentence),(struct sockaddr *) &sad，&addr_len);
    printf("FROM SERVER: %sln",modifiedSentence);
    close(clientSocket); /*close connection*/
}
```

**UDP服务端代码**

```c
/*server.c */
void main(int argc， char *argv[]){
	struct sockaddr_in sad; /* structure to hold an lP address */
    struct sockaddr_in cad;
    int serverSocket; /* socket descriptor */
    struct hostent *ptrh; /* pointer to a host table entry */
    char clientSentence[128];
    char capitalizedSentence[128];
    port = atoi(argv[1]);
    
    /* create welcoming socket at port & bind a local address*/
    serverSocket = socket(PF_INET,sOCK_DGRAM，O);
    memset((char *)&sad,0,sizeof(sad)); /* clear sockaddr structure */
    sad.sin_family = AF__INET;/* set family to Internet */
    sad.sin_addr.s_addr = INADDR_ANY; /* set the local lP address */
    sad.sin_port = htons((u_short)port);/* set the port number */
    bind(serverSocket, (struct sockaddr *)&sad,sizeof(sad));
    
    while(1){
        n=recvfrom(serverSocket, clientSentence, sizeof(clientSentence), (struct sockaddr *)&cad，&addr_len ); /*receiving messages from clients*/
        /* capitalize Sentence and store the result in capitalizedSentence*/
        n=sendto(serverSocket , capitalizedSentence,strlen(capitalizedSentence)+1，(struct sockaddr *)&cad， &addr len); /*write out the result to socket*/
    } // end of the loop, loop back and wait for another client connection.
}
```

