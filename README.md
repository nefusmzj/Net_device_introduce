# 网络设备的区别

为了更好的理解多种网络设备的功能及其区别，创建此开源项目，从而帮助大家和技术人员，更深刻的理解、应用和部署网络设备。

希望能够汲取大家智慧，欢迎大家共同完善此项目，有想法或发现Bug请提issue。

**目录**
<!-- TOC -->

- [网络设备的区别](#网络设备的区别)
    - [常规网络设备](#常规网络设备)
        - [网卡](#网卡)
            - [网卡的定义](#网卡的定义)
            - [网卡的功能](#网卡的功能)
            - [网卡的Q&A](#网卡的qa)
        - [网桥](#网桥)
            - [网桥的定义](#网桥的定义)
            - [网桥的功能](#网桥的功能)
            - [网桥的Q&A](#网桥的qa)
        - [集线器](#集线器)
            - [集线器的定义](#集线器的定义)
            - [集线器的功能](#集线器的功能)
            - [集线器的特殊应用](#集线器的特殊应用)
        - [交换机](#交换机)
            - [交换机的定义](#交换机的定义)
            - [交换机的功能](#交换机的功能)
            - [交换机的分类](#交换机的分类)
            - [网桥、集线器、交换机的区别：](#网桥集线器交换机的区别)
        - [路由器](#路由器)
            - [路由器的定义](#路由器的定义)
            - [路由器的功能](#路由器的功能)
            - [路由器的分类](#路由器的分类)
            - [路由器的Q&A](#路由器的qa)
        - [网关](#网关)
            - [网关的定义](#网关的定义)
            - [网关的功能](#网关的功能)
            - [网关的Q&A](#网关的qa)
        - [AP](#ap)
        - [AC](#ac)
        - [VPN设备](#vpn设备)
    - [虚拟设备](#虚拟设备)
        - [tun](#tun)
        - [tap](#tap)
        - [vlan](#vlan)
    - [网络安全设备](#网络安全设备)
        - [防火墙](#防火墙)
        - [IDS](#ids)
        - [IPS](#ips)
        - [网络蜜罐](#网络蜜罐)
        - [网络沙箱](#网络沙箱)

<!-- /TOC -->

## 常规网络设备

### 网卡

#### 网卡的定义

网卡，又称网络接口控制器（network interface controller，NIC），网络适配器（network adapter）。是一块被设计用来允许计算机在计算机网络上进行通讯的计算机硬件，它使得用户可以通过电缆（有线网卡）或无线（无线网卡）与互联网或局域网连接，属于OSI模型的第1层（物理层）。

每一个网卡都有一个被称为MAC地址的独一无二的48位串行号，它被写在卡上的一块ROM中。在网络上的每一个计算机都必须拥有一个独一无二的MAC地址，每个网卡厂家往往拥有一段MAC供自己的产品自行分配，因此能够根据MAC地址的前几位，一般是前6位，判断出网卡的生产厂家。

#### 网卡的功能

1. 数据的封装与解封：发送时将上一层交下来的数据加上首部和尾部，成为以太网的帧。接收时将以太网的帧剥去首部和尾部，然后送交上一层

2. 链路管理：主要是CSMA/CD（Carrier Sense Multiple Access with Collision Detection ，带冲突检测的载波监听多路访问）协议的实现

3. 编码与译码：即曼彻斯特编码与译码。

- 附加功能：

    - checksum功能：该功能在【网络适配器】-【属性】-【配置】-【高级】中进行设置。

        - TCP checksum offload: 开/关，表示网卡硬件TCP校验和功能加载和卸载

        - UDP checksum offload：开/关，表示网卡硬件UDP校验和功能加载和卸载

一般高级些的网卡才具备上述checksum offload功能及其开与关，该功能能够在网卡硬件上完成checksum的发送计算和接收校验，降低了CPU的压力。

#### 网卡的Q&A

1. 网卡上的橙色和绿色灯代表什么？

    绿色灯：连接正常指示灯

    橙色灯：数据传输指示灯

---

### 网桥

#### 网桥的定义

网桥，也叫桥接器，是连接两个局域网的一种存储/转发设备，它能将一个大的LAN分割为多个网段，或将两个以上的LAN互联为一个逻辑LAN，使LAN上的所有用户都可访问服务器。从一个网络电缆里接收信号， 放大它们，将其送入下一个电缆。相比较而言，网桥对从关卡上传下来的信息更敏锐一些。网桥是一种对帧进行转发的技术，根据MAC分区块，可隔离碰撞。网桥将网络的多个网段在数据链路层连接起来。

网桥是早期的一个过渡产物，它的功能介于集线器(HUB)和交换机(SWITCH)之间。

有线网桥与无线网桥

1. 有线网桥

将两个或多个(通常为两个)网络线缆(光缆、电缆)进行桥接，实现线缆内信号的放大，转发。

2. 无线网桥

将两个或多个(通常为两个)无线热点进行桥接，实现无线电信号放大、转发，进而扩大范围，好比电磁波的空中交换机。

#### 网桥的功能

1. LAN的分割
2. 不同LAN的连接
3. 数据链路层的连接

#### 网桥的Q&A

虚拟网桥：相对于硬件网桥设备，虚拟网桥是在计算机操作系统中，将两块网卡设置桥接，这个桥接称为虚拟网桥，该网桥的两块网卡所在网段将连接在一起。

**windows下桥接方法：** 

![image](/image/bridge.png)

**linux下桥接的方法：**

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
#退化为HUB模式
brctl setageing br0 0
```

### 集线器

#### 集线器的定义
集线器(HUB)，集线器的主要功能是对接收到的信号进行再生整形放大，以扩大网络的传输距离，同时把所有节点集中在以它为中心的节点上。采用CSMA/CD（即带冲突检测的载波监听多路访问技术)介质访问控制机制，它工作在OSI模型的第一层(物理层)。

#### 集线器的功能
集线器每个接口简单的收发比特，收到1就转发1，收到0就转发0，不进行碰撞检测。集线器属于纯硬件网络底层设备，基本上不具有类似于交换机的"智能记忆"能力和"学习"能力。它也不具备交换机所具有的MAC地址表，所以它发送数据时都是没有针对性的，而是采用广播方式发送。也就是说当它要向某节点发送数据时，不是直接把数据发送到目的节点，而是把数据包发送到与集线器相连的所有节点。

#### 集线器的特殊应用
利用能够把数据包发送到与集线器相连的所有节点的特点，集线器通常也用作抓包工具。

构建第一种场景：

某局域网有10台主机，我们需要对所有机器的网络进行监测，部署IDS设备或Wireshark抓包，就需要能够在局域网的中心节点上拿到所有主机的所有网路数据(局域网内和外发至上层网络)，有两中网络设备能够满足我的需求，一个是镜像口交换机，一个是集线器，前者价格几乎是后者的10倍甚至更多，因此在合适的情况下可以采用集线器。

构建第二种场景：

某局域网有10台主机，我们需要对该局域网对外的网络进行监测，即进出该局域网的数据。部署设备或Wireshark抓包，需要在对外链路上串联相应的设备进行数据包的转发，如果是光纤，可使用光分路器，进行抓包。如果是电缆，集线器能够实现数据的复制转发，满足监测或交换机单镜像口翻倍部署多台检测设备的需求，因此在合适的情况下可以采用集线器。

### 交换机

#### 交换机的定义

#### 交换机的功能

#### 交换机的分类

两层交换机

三层交换机

硬件交换机

软件交换机

[OpenVSwitch](http://www.openvswitch.org/)

vSphere

[SONiC P4](https://github.com/Azure/SONiC/wiki/SONiC-P4-Software-Switch)

#### 网桥、集线器、交换机的区别：

集线器各端口共享同一条背板总线

网桥上两个或多个端口分别有一条独立的交换信道

交换机多个端口分别有一条独立的交换信道

### 路由器

#### 路由器的定义

#### 路由器的功能

#### 路由器的分类

LEDE(openwrt)

#### 路由器的Q&A

哪些设备具有路由器的功能：

防火墙

路由器

### 网关

#### 网关的定义

#### 网关的功能

#### 网关的Q&A

哪些设备具有网关的功能：

防火墙

路由器


### AP

### AC

### VPN设备

## 虚拟设备

### tun

### tap

### vlan

## 网络安全设备

### 防火墙

### IDS

### IPS

### 网络蜜罐

### 网络沙箱

