---
title: TSN网络
categories: 笔记
tags: 
- TSN
- 网络
cover: https://s1.ax1x.com/2023/03/02/ppFFx54.jpg
---

# [TSN](https://zhuanlan.zhihu.com/p/342289546)

## 名词解释

### GCL：

门控列表（Gate Control List，GCL）

### CNC：

集中网络控制器（centralized network configuration，CNC）

### CUC：

集中用户控制器（centralized user configuration，CUC）

### [802.1AS](https://www.polelink.com/index.php?m=content&c=index&a=show&catid=93&id=53)：

通用精确时间协议（Generalized Precision Time Protocol），将为汽车、工业自动化控制等领域实现精确时间的测量。

### [Linux Traffic Control](https://blog.csdn.net/qinyushuang/article/details/46611709):

Linux TC(Traffic Control) 众所周知，在互联网诞生之初都是各个高校和科研机构相互通讯，并没有网络流量控制方面的考虑和设计，IP协议的原则是尽可能好地为所有数据流服务，不同的数据流之间是平等的。然而多年的实践表明，这种原则并不是最理想的，有些数据流应该得到特别的照顾，比如，远程登录的交互数据流应该比数据下载有更高的优先级。

### [NETCONF](https://info.support.huawei.com/info-finder/encyclopedia/zh/NETCONF.html):

网络配置协议NETCONF（Network Configuration Protocol）为网管和网络设备之间通信提供了一套协议，网管通过NETCONF协议对远端设备的配置进行下发、修改和删除等操作。网络设备提供了规范的应用程序编程接口API（Application Programming Interface），网管可以通过NETCONF使用这些API管理网络设备。
NETCONF是基于可扩展标记语言XML（Extensible Markup Language）的网络配置和管理协议，使用简单的基于RPC（Remote Procedure Call）机制实现客户端和服务器之间通信。客户端可以是脚本或者网管上运行的一个应用程序。服务器是一个典型的网络设备。

云时代对网络的关键诉求之一是网络自动化，包括业务快速按需自动发放、自动化运维等。传统的命令行和SNMP已经不适应云化网络的诉求。在网络自动化方面，NETCONF越来越受欢迎，并被广泛采用。

### [OPC UA](https://zhuanlan.zhihu.com/p/430243728):

在网络化、标准化或网络安全方面，对工业网络的要求正以非凡的速度增长。在这些问题重重的领域，基于以太网的 OPC UA（Open Platform Communications – Unified Architecture，开放平台通信 - 统一架构）通信标准正在快速发展。凭借其集成的安全机制，独立于供应商和平台的特性， OPC UA 为数字化提供了优异基础条件。

**交换机**

Gbps:

也称交换带宽，是衡量交换机总的数据交换能力的单位，以太网是IEEE802.3以太网标准的扩展，传输速度为每秒1024兆位(即1Gbps)。

## 调度模型1.0
TSN调度问题简化：
![TSN调度问题](https://s1.ax1x.com/2023/03/03/ppA8keU.png)
```txt
Q:通过调整每个f的一个开始发送时间，把周期性数据给错开，假如发送周期都是离散的数值，每个数据发送占一个周期的时间，就取一个所有周期的最大公因数，在这个最大公因数长度的时间段中依次发送初始数据，计算offset就不会冲突了?

A:最大公约数这个是一个方法，可以先这样做。显示上也足够了。之后的展示更加复杂后，若没有最大公约数，这个方法就行不通了。

Q:但是如果没有最大公约数的话，会不会有无法避免冲突的情况产生呀

A:肯定有无法避免的情况产生。那就是调度不成功的情况，例如说有1000个流，你只能调度600个，那就展示600个的结果就可以了。
数据包在网络中传输经过了传输，传播，处理以及排队时延。1m的链路长度用于计算传播时延。

Q:在TSN提前计算好了冲突解决，就不存在排队时延了吗？
A:TSN交换机可以控制流什么时候被传输，因此调度就是能够控制排队时延。简单来讲，我给你的题目是计算出offset以完成无等待传输（即排队时延为0），这样最简明易懂。更复杂的就是你可以有目的的让某些流延迟，以传输更为紧急的流量。

Q:那我们目前是不需要考虑优先等级吗，先满足能正常调度，然后做出展示就好了？
A:目前先不考虑，这个在之后的细节丰富中增加。例如说当网络负载变大时，低优先级的受到影响，但是依然能保持高优先级的传输。
```