# 网络设备的区别

为了更好的理解多种网络设备的功能及其区别，创建此开源项目，希望能够汲取大家智慧，完善此项目，从而帮助大家和技术人员，更深刻的理解、应用和部署网络设备。

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
        - [交换机](#交换机)
        - [路由器](#路由器)
        - [网关](#网关)
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
2. 无线网桥

#### 网桥的功能

1. LAN的分割
2. 不同LAN的连接
3. 数据链路层的连接

#### 网桥的Q&A

虚拟网桥

windows下桥接方法

linux下桥接的方法

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

### 交换机

两层交换机

三层交换机

硬件交换机

软件交换机

[OpenVSwitch](http://www.openvswitch.org/)

vSphere

[SONiC P4](https://github.com/Azure/SONiC/wiki/SONiC-P4-Software-Switch)

网桥、集线器、交换机的区别：

集线器各端口共享同一条背板总线

网桥上两个或多个端口分别有一条独立的交换信道

交换机多个端口分别有一条独立的交换信道


### 路由器

LEDE(openwrt)

### 网关

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

