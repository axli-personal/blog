---
title: "Virtual Machine Network"
date: 2022-07-26T12:00:00+08:00
summary: "三种虚拟机网络: 桥接, NAT, 仅主机"
---

# 桥接

虚拟机与物理网卡间存在链路, 能够和主机一样从DHCP服务器获取IP.

# NAT

虚拟机与虚拟网卡间存在链路, 能够从虚拟DHCP服务器获取IP, 并通过虚拟NAT访问外部网络.

# 仅主机

虚拟机与虚拟网卡间存在链路, 能够从虚拟DHCP服务器获取IP, 无法访问外部网络.