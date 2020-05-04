### 网络设备学习笔记

同学们好，请问你们知道 HUB 与交换机的区别吗？为什么有些时候还要用 HUB ？知道 IDS 与IPS 的区别么？PC 可以当交换机或路由器吗？大家多是从事计算机、软件、通信或相关行业的，怎么才能对网络设备有全面的了解，在客户有需求时能够选用相应的网络设备呢？请带着问题学习本篇 Chat 吧。

本 Chat 内容是本人在工作中，对常见的网络设备的学习笔记和经验总结，介绍了交换机、路由器、防火墙、AP、IDS 等多种网络设备的定义、功能以及一些使用技巧。

本文旨在帮助同学们梳理网络设备定义、功能特点、差异，让同学们在工作学习乃至生活中，对网络设备的选择、架设有系统的认知，并不对具体设备配置进行教程式的讲解。比较适合对网络设备有一定基础，但对其定义、功能、差异又有些模糊的同学。并不适合专业的网络工程师，也不适合看完本篇 Chat 就想为机房、小区甚至城市建设网络的同学。

在学习和编写此笔记过程中大量参考了 Kevin R. Fall 等作者的《TCP/IP详解 卷1 卷2 卷3》和谢希仁老师的《计算机网络》，在此表示感谢。

本文覆盖的网路设备脑图如下：

![devicelistmind](image/mindmap.png)

| 常规网络设备                | 虚拟网络设备            | 网络安全设备                      |
| --------------------------- | ----------------------- | --------------------------------- |
| ![devicenic](image/nic.png) | ![VPN](image/vpn.png)   | ![Firewall](image/firewall.png)   |
| ![Bridge](image/bridge.png) | ![tun](image/tun.png)   | ![ids](image/ids.png)             |
| ![HUB](image/hub.png)       | ![Vlan](image/vlan.png) | ![ips](image/ips.png)             |
| ![switch](image/switch.png) |                         | ![honeypots](image/honeypots.png) |
| ![Router](image/router.png) |                         | ![sandbox](image/sandbox.png)     |
| ![Gateway](image/gw.png)    |                         |                                   |
| ![AP](image/ap.png)         |                         |                                   |
| ![AC](image/ac.png)         |                         |                                   |




#### 常规网络设备

##### 网卡

![devicenic](image/nic.png)

###### 网卡的定义

网卡（*Network Interface Controller*，*NIC*，网络接口控制器）或（*Network Adapter*，网络适配器）。是用来允许计算机在计算机网络上进行通讯的计算机硬件，使用户可以通过线缆（有线 RJ45 网卡、光纤卡）或电磁波（无线网卡）与互联网或局域网连接，其工作在 OSI 模型的第 1、2 层（物理层、数据链路层）。

每个网卡都有独一无二的 48 位串号即 MAC 地址，存储于网卡的 ROM 中，因此网络上的每台计算机的网卡拥有独一无二的 MAC 地址。

###### 网卡的功能

1. 数据的封装与解封：

   **发送时**将其上一层（网络层）交下来的数据加上以太首部（MacDst，MacSrc，EthProtocol）和尾部（FCS），成为以太网的帧；

   **接收时**将以太网的帧剥去首部和尾部，然后送交上一层（网络层）。

常见 EthProtocol 有：

|IPv4|IPv6|ARP|PPPoE|802.1Q|
|--|--|--|--|--|
|0x0800|0x08DD|0x0806|0x8864|0x8100|

2. 链路管理：主要是 CSMA/CD（*Carrier Sense Multiple Access with Collision Detection*，带冲突检测的载波监听多路访问）协议的实现。

3. 编码与译码：即曼彻斯特编码与译码。

4. 附加功能：

- checksum 功能：该功能在【网络适配器】-【属性】-【配置】-【高级】中进行设置。
    - TCP checksum offload：开/关，表示网卡硬件 TCP 校验和功能加载和卸载；
    - UDP checksum offload：开/关，表示网卡硬件 UDP 校验和功能加载和卸载。
    

*此功能包含网络层甚至传输层，一般高级些的网卡才具备上述 checksum offload 功能及其开与关，该功能能够在网卡硬件上完成 checksum 的发送计算和接收校验，降低了 CPU 的压力。*

###### 网卡的Q&A

1. 网卡上的橙色和绿色灯通常情况下代表什么？

    绿色灯：连接正常指示灯；

    橙色灯：数据传输指示灯。

2. 如何根据 MAC 地址判断网卡的厂家或品牌？

    每个网卡厂家往往拥有一段 MAC 供自己的产品自行分配，因此能够根据 MAC 地址的前几位（一般是前 6 位），判断出网卡的生产厂家。使用 [Wireshark](https://www.wireshark.org/) 抓包时，可以看到网卡的厂家信息。

---
##### 网桥

![Bridge](image/bridge.png)

###### 网桥的定义

网桥（*Net Bridge*，桥接器），是 LAN 的一种转发交换设备，用于 LAN 的划分或互连。网桥从一个网络接收电缆信号（无线电磁波信号），放大它们，将其送入下一个电缆。网桥是一种对帧进行转发的技术，将网络的多个网段在数据链路层连接起来，根据 MAC 分区块，可隔离冲突域，是二层的网络设备。

网桥是早期的一个过渡产物，它的功能介于集线器（HUB）和交换机（SWITCH）之间。

###### 网桥的功能

1. LAN 的分割与互连

    它能将一个大的 LAN 分割为多个网段，或将两个以上的 LAN 互联为一个逻辑 LAN ，使 LAN 上的所有用户都可访问服务器。

2. 物理层信号的放大

    它能将一个电缆里接收到的信号，放大信号，再转送到下一个电缆中；或将空间的无线网络的电磁波接收，放大信号，再发射到空间中去。

3. 数据链路层的连接

    网桥是一个二层网络设备，它能对网络帧进行转发，根据 MAC 地址进行有选择的转发，类似于二层交换机。

###### 网桥的Q&A

**网桥的分类：**

1. 按照信号特点：

- 有线网桥：将两个或多个（通常为两个）网络线缆（光缆、电缆）进行桥接，实现线缆内信号的放大，转发；
- 无线网桥：将两个或多个（通常为两个）无线热点进行桥接，实现无线电信号放大、转发，进而扩大范围，好比电磁波的空中交换机。

2. 按照设备形态

- 硬件网桥：是专门硬件设备；
- 软件网桥（虚拟网桥）：由计算机的网桥软件来实现（计算机上会安装多个网络适配器）。

**windows 下桥接方法：**

同时选择两个适配器，在其中一个网桥上鼠标右键，选择桥接。

![wincreatebridge](image/wincreatebridge.png)

**linux 下桥接的方法：**

```shell
#安装网桥管理软件
apt-get install bridge-util
#创建网桥
brctl addbr br0
#添加网卡
brctl addif br0 enp2s0
brctl addif br0 enp3s0
#查看网桥状态
brctl show
```
*网桥、集线器、交换机三者有何区别，将在讲解完这三者之后，给出讲解。*

---

##### 集线器

![HUB](image/hub.png)

###### 集线器的定义
集线器（HUB），是一种将所有节点集中在以它为中心的节点上并实现网络互连的设备，采用 CSMA/CD （带冲突检测的载波监听多路访问技术）介质访问控制机制，它工作在 OSI 模型的第一层（物理层）。

集线器每个接口简单的收发比特，收到 1 就转发 1，收到 0 就转发 0，不进行碰撞检测。集线器属于纯硬件网络底层设备，基本上不具有类似于交换机的”智能记忆“能力和”学习“能力。它也不具备交换机所具有的 MAC 地址表，所以它发送数据时都是没有针对性的，而是采用广播方式发送。

###### 集线器的功能

集线器的主要功能是对接收到的信号进行再生整形放大，以扩大网络的传输距离，同时把所有节点集中在以它为中心的节点上。当它要向某节点发送数据时，不是直接把数据发送到目的节点，而是把数据包发送到与集线器相连的所有节点即广播方式发送。

###### 集线器的特殊应用
利用能够把数据包发送到与集线器相连的所有节点的特点，集线器通常也用作抓包工具。

1. 场景一：

    某局域网有 10 台主机，我们需要对所有机器的网络进行监测，部署 IDS 设备或 Wireshark 抓包，就需要能够在局域网的中心节点上拿到所有主机的所有网路数据（局域网内以及外发至上层网络），有两中网络设备能够满足我的需求，一个是镜像口交换机，一个是集线器，前者价格几乎是后者的 10 倍甚至更多，因此在合适的情况下可以采用集线器。

2. 场景二：

    某局域网有 10 台主机，我们需要对该局域网对外的网络进行监测，即进出该局域网的数据。部署设备或 Wireshark 抓包，需要在对外链路上串联相应的设备进行数据包的转发，如果是光纤，可使用光分路器，进行抓包。如果是电缆，集线器能够实现数据的复制转发，满足监测或交换机单镜像口翻倍部署多台检测设备的需求，因此在合适的情况下可以采用集线器。

*网桥、集线器、交换机三者有何区别，将在讲解完这三者之后，给出讲解。*

---
##### 交换机

![switch](image/switch.png)

###### 交换机的定义

交换机（Switch）是一种用于电（光）信号转发的网络设备。它可以为接入交换机的任意两个网络节点提供独享的电信号通路。工作在 OSI 模型的第二层（数据链路层）及以上。

###### 交换机的功能

交换机最基本功能是为接入其端口的任意两个端口间提供虚拟专线连接。主要功能包括物理编址、网络拓扑结构、错误校验、帧序列以及流控。还具备了一些新的功能，如 VLAN、链路汇聚，甚至还具有防火墙功能。上述这些功能集线器无法比拟的，也是交换机与集线器的差异所在。

1. 学习功能

    以太网交换机了解每一端口相连设备的 MAC 地址，并将地址同相应的端口映射起来存放在交换器缓存中的 MAC 地址表中。

    我们在网桥一节中，可以通过设置”学习功能（setageing）“的时间来控制网桥工作在”具有的学习功能的交换机模式“，还是”无学习功能的 HUB 模式“。
    
    ```shell
    #设置学习时间
    brctl setageing br0 60
    #退化为HUB模式
    brctl setageing br0 0
    ```

2. 转发过滤

    当一个数据帧的目的地址在 MAC 地址表中有映射时，它被转发到连接目的节点的端口而不是所有端口（如该数据帧为广播/组播帧则转发至所有端口）。若在端口和 MAC 映射表中不存在该 MAC，则广播所有端口，等待特定端口产生回复数据后，更新端口 MAC 映射表。

3. 消除回路

    当交换器包括一个冗余回路时，以太网交换器通过生成树协议（STP）避免回路的产生，同时允许存在后备路径。

###### 交换机的分类

交换机分类多种多样，可以按照光电特性、速度、场合、用途而分为不同的种类。

1. 按网路信号类型可分为：光交换机、电交换机。

2. 按工作 OSI 层可分为：两层交换机、三层交换机、四层交换机。

    - 两层交换机：顾名思义其工作拥有 OSI 两层的工作能力，即物理层和数据链路层。是目前非常成熟的交换机技术，这种交换机能够完成端口和 MAC 的映射，识别数据包中 MAC 地址信息，根据 MAC 地址和端口 MAC 映射，将数据帧发往特定的端口。
    - 三层交换机：三层交换机拥有 OSI 三层的工作能力，在传统两层交换机的基础上，增加了网络层的相关功能。具体体现在对网络数据包中IP进行了识别，能够实现不同子网间数据包的转发。
    - 四层交换机：四层交换机拥有 OSI 四层的工作能力，在三层交换机的基础上，增加了传输层的相关功能。具体体现在对网路数据包中 TCP/UDP 的端口进行了识别。

3. 按端口速度可分为：百兆交换机、千兆交换机、万兆交换机。

4. 按运行平台可分为：硬件交换机，软件交换机。

    - 硬件交换机：是指具有交换机外观，内部具有交换机电路，交换机芯片的，实现交换机功能的硬件设备。
    - 软件交换机：是指在 PC 机或 ARM 版上（非交换机专用芯片平台），通过纯软件的方式，建立虚拟连接，实现数据转发。常见软件交换技术方案有：
        - [OpenVSwitch](http://www.openvswitch.org/)
        - [vSphere](https://www.vmware.com/cn/products/vsphere.html)
        - [SONiC P4](https://github.com/Azure/SONiC/wiki/SONiC-P4-Software-Switch)
        
        因此，PC 或服务器，也是可以作为交换机来使用的，当然性价比是需要考量的因素了。

**软交换技术的优劣**

优势：高度可控可编程，可以很容易的实现端口动态管理。在虚拟化、云等场景下可以低成本部署，资源分配、管理，甚至二次开发。

劣势：同等成本情况下，性能配置不及硬件交换机，无硬件加速，依赖运行平台的综合参数。

**软交换的应用：** 虚拟化、公有云

##### 网桥-集线器-交换机的区别

|设备类型|背板总线|交换通道|转发延时|带宽|OSI层|备注|
|--|--|--|--|--|--|--|
|网桥|多条|各端口独立拥有|大|高|1、2 层|有目的转发|
|集线器|一条|一条|大|小|1 层|无目的广播|
|交换机|多条|各端口独立拥有|小|大|1、2（3、4）层|有目的转发|

对于交换机来说，当节点 A 向节点 D 发送数据时，节点 B 可同时向节点 C 发送数据，而且这两个传输都享有网络的全部带宽，都有着自己的虚拟连接。

假使 A->D， B->C 这里使用的是 10Mbps 的以太网交换机，那么该交换机这时的总流通量就等于 2 × 10Mbps = 20Mbps，而使用 10Mbps 的共享式集线器时，一个集线器的总流通量也不会超出 10Mbps。

---

##### 路由器

![Router](image/router.png)

###### 路由器的定义

**路由的定义：**
路由是指分组从源到目的地时，决定端到端路径的网络范围的进程.

**路由器的定义：**
路由器在网路层实现网间互联，它可以实现同类型或不同类型的网络互连，包括 LAN 与 LAN，LAN 与 WAN，WAN 与 WAN 的互连。是一个具有多个输入端口和多个输出端口的专用计算机，其功能是转发分组，它把从某一输入端口收到的分组按照分组所携带的目的地址从适当的输出端口转发出去。

![routestructure](image/routerstructure.png)

###### 路由器的功能

**路由选择:** 路由选择部分也叫做控制部分，其核心构件是路由选择处理机，路由选择处理机的任务是根据所选定的路由器选择协议构造出路由表，同时经常或定期地和相邻路由器交换路由信息而不断地更新和维护路由表。

**分组转发:** 包括交换结构、输入端口、输出端口。交换结构是根据转发表对分组进行处理，将某个输入端口进入的分组从一个合适（即按照上述的路由选择中的路由表）的输出端口转发出去，输入输出端口包括物理层、数据链路层、网络层的处理模块，完成比特-->帧-->分组的“抽丝剥茧”过程。

上述两个功能构成路由器核心功能，即常说的 NAT （网络地址转换）功能。

*分组转发只涉及到一个路由器，在路由器内部工作；路由选择涉及到很多路由器。*

**其他功能：**

1. 端口转发，也称端口映射：

    问：如果一个 WAN 口的主机，需要**主动**连接 LAN 口的主机 IP:Port，（例如：想在 LAN 口架设一台服务器，WAN 口要请求服务器），怎么能够实现呢？

    答：将一个 WAN 口 Port 与 LAN 口主机 IP:Port 设置为转发链路，类似静态路由，此时 WAN 口主机向路由器 WAN 口 IP:Port 发送数据，路由器发在该包头信息存在于端口转发表中，于是将该数据包按照目的为 LAN 口的 IP:Port 的规则进行 NAT 转换，则该包会发往 LAN 口主机的 IP:Port 上。

2. DMZ（*Demilitarized Zone*，原意：非军事隔离区）:

    实际上就是把内网中的一台主机完全暴露给互联网，开放所有端口，等同于全部端口映射。等于直接使用公网 IP。

3. DDNS（*Dynamic Domain Name Server*，动态域名解析）:

    是将用户的动态 IP 地址映射到一个固定的域名解析服务上，用户每次连接网络的时候客户端程序就会通过信息传递把该主机的动态 IP 地址传送给位于服务商主机上的服务器程序，服务器程序负责提供 DNS 服务并实现动态域名解析。

###### 路由器的分类

1. 按信号的发送方式分类：有线路由器、无线路由器；

2. 按用途分类：企业级路由器、家用路由器；

3. 按固件类型分类：开源路由器、闭源路由器；

4. 按智能性分类：智能路由器、非智能路由器。

###### 路由器的Q&A

1. 哪些设备具有路由器的功能：
    - 防火墙
    - 路由器
    - L3\L4 层交换机

2. 高带机量的实验室、宾馆、教室的**低成本 ** Wi-Fi 解决方案：
    - 软路由+胖 AP
    - 软路由 + AC + 瘦 AP

    AP 与 AC 相关知识在下文会有介绍。
    
3. 智能路由器开源操作系统
    - [OpenWRT(LEDE)](https://openwrt.org/)
    - [DD-WRT](https://dd-wrt.com/)
    - [Tomato-WRT](http://www.polarcloud.com/tomato)

4. 家用的无线路由器属于什么：

   家用路由器实际上是一个复合设备，是路由器、交换机、网关、AP、AC 有些还包含有防火墙、VPN 等多模块与一身的网络设备。说句题外话，家用路由怎么做到如此低成本呢？ 其实家用路由对性能、稳定性等要求并不高，百兆、千兆，也就 10 几台设备在用，因此使路由专用 SOC 方案可以把成本做到很低。

---

##### 网关

![Gateway](image/gw.png)

###### 网关的定义

网关（*Gateway*，网间连接器），网关在网络层以上实现网络互连，是最复杂的网络互连设备之一，仅用于两个高层协议不同的网络互连。网关是一种充当转换重任的计算机系统或设备。使用在不同的通信协议、数据格式或语言，甚至体系结构完全不同的两种系统之间，网关是一个翻译器。

###### 网关的功能

网关用于连接两个不同的网络，当同一网段内的主机进行通信，不需要网关的介入；当不同网段的主机进行通信时，数据包会先发往网关，再由网关进行转发。

例如：

我们有两个网络，网络一地址：192.168.0.0/24，网络二地址：192.168.1.0/24，网关地址：192.168.2.254。

A 主机 IP：192.168.1.1

B 主机 IP：192.168.1.2

C 主机 IP：192.168.0.1

D 主机 IP：192.168.0.2

E 为网关：192.168.2.254

路径图：

|*|A|B|C|D|
|--|--|--|--|--|
|A|--|B-->A|C-->E-->A|D-->E-->A|
|B|A-->B|--|C-->E-->B|D-->E-->B|
|C|A-->E-->C|B-->E-->C|--|D-->C|
|D|A-->E-->D|B-->E-->D|C-->D|--|

*因此，对于跨网抓包，我们看到包内容中，通常是网关的 MAC 和 IP 地址*

###### 网关的 Q&A

1. 网关是什么设备：

    网关是一个概念，凡是具有网关功能的设备都可以叫做网关，可以是路由器、计算机甚至交换机。

2. 哪些设备可以充当网关：
    - 防火墙
    - 路由器
    - 计算机
    - 交换机
    
---
##### AP

![AP](image/ap.png)

###### AP的定义
AP（*Access Piont*，无线接入点），将有线网络转换成无线网络，是无线网和有线网之间沟通的桥梁。常见的 AP 按照工作频率划分为 2.4GAP 和 5GAP。

###### AP的功能
将有线网络转换成无线网络，使有线网络与无线网络能够互联互通。评估一个 AP 性能的主要参数包括：有线速率，无线速率，带机量等。

例如某品牌 AP 参数表如下：
- 有线速率：1000Mbps；
- 无线速率：1200Mbps；
- 带机量：50台。

###### AP的 Q&A

1. AP 与无线网桥的区别？

无线网桥是通过无线传输方式实现在两个或多个网络之间进行通信的桥梁，因此同时具备无线信号的发射与接收功能。无线网桥产品定位于远距离无线传输数据，因此其天线通常是定向的，传输距离远大于无线 AP 。无线网桥除了可以将两个无线网络桥接外，也可以将有线网络与无线网络连接。而 AP 作为无线接入点，是实现在有线网络和无线网络之间进行通信的桥梁。

2. 什么是 FitAP（瘦 AP）？

FitAP 是 AP 的一种工作模式，在该模式下，AP 不具有配置能力，不提供 web 管理页面，只接受 AC 的控制和命令下发。因此 FitAP 直接接入路由器或交换机上，是无法工作的，一定要配合 AC。适合大规模部署和管理。

3. 什么是 FatAP（胖 AP）？

Fat 是 AP 的另一种工作模式，在该模式下，AP 具有配置能力，提供独立 web 管理页面，可独立工作。因此 FatAP 直接接入路由器或交换机上即可工作。适合小范围小规模部署和管理。

---
##### AC

![AC](image/ac.png)

###### AC的定义

AC（*Access Point Controler*，无线接入控制器），用于集中化控制无线 AP，是一个无线网络的核心，负责管理无线网络中的所有 AP。

###### AC的功能

对 AP 下发配置、修改参数、接入安全控制。

###### AC的优势
1. 灵活的组网方式和优秀的扩展性；
2. 智能的 RF 管理功能，自动部署和故障恢复；
3. 集中的网络管理；
4. 强大的漫游功能支持；
5. 负载均衡；
6. 强大的接入和安全策略控制。

---

#### 虚拟设备

##### VPN设备

![VPN](image/vpn.png)

###### VPN的定义
VPN（*Virtual Private Network*，虚拟专用网），利用公用的因特网作为某机构各专用网之间通信的载体，这样的专用网称作虚拟专用网。

“虚拟”表示“好像是”，但实际并不是，因为并没有使用专线而是通过公用的因特网来连接分散在各场所的本地网络。VPN 只是在效果上和真正的专用网一样。

“专用网”是因为这种网络是为本机构的主机用于机构内部的通信，而不是用于和网络外的本机构的主机通信。如果专用网不同网点之间的通信必须经过共用的因特网，但又有保密要求，那么所有通过因特网传送的数据都必须要经过加密。

###### VPN的功能
- VPN 是一种远程访问技术，通过共用的因特网实现本机构的各场所子网间的通信；

- VPN 隧道两边的用户，感觉好像就是在使用同一个本地网络。

###### VPN的Q&A

- 通常带有VPN功能的设备:
  - 路由器
  - 防火墙
  - 交换机
  - VPN 设备

看完 VPN，同学们应该了解了，VPN 是一种虚拟专用网技术，并不仅是科学上网的工具。

---

##### TUN&TAP

![TUN&TAP](image/tun.png)

###### TUN&TAP的定义

TUN&TAP 都是虚拟网络设备，是一种纯软件的实现，为 Linux 操作系统所特有。TUN 是网络层虚拟设备，处理 IP 数据包；TAP 是数据链路层虚拟设备，处理数据链路层数据包。

###### TUN&TAP的功能
TUN&TAP 设备的用处是将协议栈中的部分数据包转发给用户空间的应用程序，给用户空间的程序一个处理数据包的机会。于是比较常用的数据压缩，加密等功能就可以在应用程序 B 里面做进去，TUN&TAP 设备最常用的场景是 VPN，包括 tunnel 以及应用层的 IPSec 等。

###### TUN&TAP工作原理

```+----------------------------------------------------------------+
|                                                                |
|  +--------------------+      +--------------------+            |
|  | User Application A |      | User Application B |<-----+     |
|  +--------------------+      +--------------------+      |     |
|               | 1                    | 5                 |     |
|...............|......................|...................|.....|
|               ↓                      ↓                   |     |
|         +----------+           +----------+              |     |
|         | socket A |           | socket B |              |     |
|         +----------+           +----------+              |     |
|                 | 2               | 6                    |     |
|.................|.................|......................|.....|
|                 ↓                 ↓                      |     |
|             +------------------------+                 4 |     |
|             | Newwork Protocol Stack |                   |     |
|             +------------------------+                   |     |
|                | 7                 | 3                   |     |
|................|...................|.....................|.....|
|                ↓                   ↓                     |     |
|        +----------------+    +----------------+          |     |
|        |      eth0      |    |      tun0      |          |     |
|        +----------------+    +----------------+          |     |
|    10.32.0.11  |                   |   192.168.3.11      |     |
|                | 8                 +---------------------+     |
|                |                                               |
+----------------|-----------------------------------------------+
                 ↓
         Physical Network
```

---

##### VLAN

![VLAN](image/vlan.png)

###### VLAN的定义
VLAN（*Virtual Local Area Network*，虚拟局域网），是一组逻辑上的设备和用户，这些设备和用户并不受物理位置的限制，可以根据功能、部门及应用等因素将它们组织起来，相互之间的通信就好像它们在同一个网段中一样，由此得名虚拟局域网。VLAN 是一种比较新的技术，工作在 OSI 参考模型的第 2 层和第 3 层，一个 VLAN 就是一个广播域，VLAN 之间的通信是通过第3层的路由器来完成的。VLAN 的协议标准是 802.1Q。

###### VLAN的功能

VLAN 用于把统一物理局域网内的不同用户逻辑地划分成不同的广播域，每一个 VLAN 都包含一组有着相同需求的计算机工作站，与物理上形成的 LAN 有着相同的属性。由于它是从逻辑上划分，而不是从物理上划分，所以同一个 VLAN 内的各个工作站没有限制在同一个物理范围中，即这些工作站可以在不同物理 LAN 网段。

**VLAN的优势**

与传统的局域网技术相比较，VLAN 技术更加灵活，它具有以下优点：
1. 网络设备的移动；
2. 添加和修改的管理开销减少；
3. 可以控制广播活动；
4. 可提高网络的安全性。

设置 VLAN 的最主要设备：路由器、三层交换机

---

#### 网络安全设备

##### 防火墙

![Firewall](image/firewall.png)

###### 防火墙的定义

防火墙（*Firewall*），作为一种访问控制技术，通过严格控制进出网络边界的分组，禁止任何不必要的通信，从而减少潜在入侵的发生，尽可能降低这类安全威胁所带来的安全风险。防火墙是一种特殊编程的路由器，安装在一个网点和网络的其余部分之间，目的是实施访问控制策略。

防火墙外面的网络称为：**不可信网络**。

防火墙里面的网络成为：**可信网络**。

###### 防火墙的功能

1. 分组过滤：根据过滤对着对进出内部网络的分组执行转发或丢弃，过滤规则基于分组的网络层或传输层的首部信息。及过滤五元组（源/目的 IP 地址、源/目的 Port、协议）

2. 应用网关：所有进出网络的应用程序报文都必须经过网关，应用网关在应用层打开该报文，查看该请求是否合法（可根据应用层用户表示 ID 或其他应用层信息）。如果合法，应用网关以客户进程的身份将请求的报文转给原始服务器，如果不合法，报文被丢弃。

###### 防火墙的Q&A

开源防火墙系统
1. [pfsense](https://www.pfsense.org/)
2. [opensense](https://opnsense.org/)

开源防火墙软件
1. iptable
2. ufw

---

##### IDS

![IDS](image/ids.png)

###### IDS的定义

IDS（*Intrusion Detection System*，入侵检测系统），IDS 对进入网络的分组执行深度分组检查，当观察到可以分组时，向网络管理员发出告警。

###### IDS的功能
检测多种已知网络攻击，包括：网络映射、端口扫描、DoS 攻击、病毒和蠕虫、系统漏洞攻击等。

###### IDS工作模式分类

1. 基于特征的 IDS

    基于特征的 IDS 维护一个所有已知攻击标志性特征的数据库，每个特征是一个与某种入侵活动关联的规则集，这些规则可能基于单个分组的首部字段值或数据中特定比特串，或者与一些列分组有关。当发现有与某种攻击特征匹配的分组或分组序列时，则认为可能检测到某种入侵行为（但基于特征的 IDS 智能检测已知攻击）。这些特征和规则通常由网络安全专家生成，我们在 GitHub 等开源网站上可以下载到他人整理好的规则集。

2. 基于异常的 IDS

    基于异常的 IDS 通过观察正常运行的网络流量，学习正常流量的统计特性和规律，当检测到网络中流量某种统计规律特征不符合正常情况时，则认为可能发生了入侵行为。但这种模式显然准确性很低且缺乏证据。

###### IDS的Q&A

开源 IDS 引擎
1. [snort](https://www.snort.org/)
2. [suricata](https://suricata-ids.org/)

---

##### IPS

![ipstag](image/ips.png)

###### IPS的定义

IPS 是在 IDS 功能的基础上增加阻断功能，但其阻断功能通常不及防火墙，属于 IDS 与防火墙之间的一种网络安全设备。

###### IPS的Q&A

开源 IPS 引擎
1. [snort](https://www.snort.org/)
2. [suricata](https://suricata-ids.org/)

---

##### 网络蜜罐

![honeypots](image/honeypots.png)

###### 蜜罐的定义

蜜罐（*Honeypots*）技术本质上是一种对攻击方进行欺骗的技术，通过布置一些作为诱饵的主机、网络服务或者信息，诱使攻击方对它们实施攻击，从而可以对攻击行为进行捕获和分析，了解攻击方所使用的工具与方法，推测攻击意图和动机，能够让防御方清晰地了解他们所面对的安全威胁，并通过技术和管理手段来增强实际系统的安全防护能力。

###### 网络蜜罐

蜜罐可以部署在主机操作系统上，另一种作为网络设备部署在网络中称为网络蜜罐。

###### 蜜罐的Q&A

用白话来讲，蜜罐就是防守方部署的一台故意让攻击方攻入的装置，用来捕获攻击方的阴谋诡计和推测侦查攻击方的。也可以说是防守方的钓鱼行为。

---

##### 网络沙箱

![sandboxtag](image/sandbox.png)

###### 沙箱的定义

沙箱（*Sandbox*）是一个虚拟系统程序，允许你在沙盘环境中运行浏览器或其他程序，因此运行所产生的变化可以随后删除。它创造了一个类似沙盒的独立作业环境，在其内部运行的程序并不能对硬盘产生永久性的影响。其为一个独立的虚拟环境，可以用来测试不受信任的应用程序或上网行为。

###### 网络沙箱

沙箱可以部署在主机操作系统上，另一种作为网络设备部署在网络中称为网络沙箱。

###### 沙箱的Q&A

1. 我们日常网络有用到沙箱么？

   答案是肯定的，沙箱离我们并不遥远，并不都是高安保的网络才有的网络设备。像我们电脑的杀毒软件、手机操作系统、二维码扫描等，很多都具备沙箱功能或用到了沙箱原理。

   - 像安卓操作系统，一些厂商的沙箱功能可以再下载未知文件，安装位置 APP 时，先进入沙箱执行；
   - 二维码我们在扫描之前，通常不知道二维码背后是付款码、公众号还是恶意网站，那么在我们扫描二维码后，此二维码的解析和链接跳转工作先在沙箱中运行，确定安全后再呈现出来，此过程通常较快，我们感知不到而已。本人就曾提出过二维码沙箱概念的专利，无奈专利检索时发现已经有人抢先一步了。

---

#### 结语

通过本次 Chat，我们学习了常规网络设备、虚拟网路设备以及网络安全设备。梳理了网络设备定义、功能特点、差异；学习了网络设备的选择方法。并没有具体设备配置展开讲解，因为这不是本篇 Chat 的主旨，设备的配置方法也无法在有限的篇幅内覆盖全面，望大家能够理解。网络设备不计其数，我建议学习的过程要广泛了解，术业专攻，这也是本 Chat 的根本目的，知识是无穷无尽的，重点是掌握 Get 知识的方法。

本 Chat 对网络设备的讲解就到这里，感谢大家的学习与支持。


