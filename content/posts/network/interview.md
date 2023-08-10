---
title: "Interview"
date: 2023-02-06T20:27:22+08:00
tags: []
categories: []
summary: "TODO"
---

## 4 Layer Model

Application: 应用间双向可靠的数据流
Transport: 正确有序的发送端到端数据, 拥塞控制.
Network: 尽力而为的交付端到端数据.
Link: 在单一链路上发送数据.

## TCP三次握手

第一次握手：客户端发送网络包，服务端收到了。服务端得出结论：客户端的发送能力、服务端的接收能力是正常的。

第二次握手：服务端发包，客户端收到了。客户端得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的。不过此时服务器并不能确认客户端的接收能力是否正常。

第三次握手：客户端发包，服务端收到了。服务端得出结论：客户端的接收、发送能力正常，服务器自己的发送、接收能力也正常。

三次握手能防止历史连接的建立，能减少双方不必要的资源开销，能帮助双方同步初始化序列号

不使用「两次握手」和「四次握手」的原因：

「两次握手」：无法防止历史连接的建立，会造成双方资源的浪费，也无法可靠的同步双方序列号；
「四次握手」：三次握手就已经理论上最少可靠连接建立，所以不需要使用更多的通信次数。

## TCP四次挥手

## TCP的粘包、拆包以及解决方案

## HTTP/1.0，1.1，2.0

## TCP与UDP的区别

## 流量控制

## 访问网页全过程

* DHCP
* ARP
* DNS: 只指路不带路
* HTTP
* TCP
* IP: 路由表.
* MAC


## 面经

* 排序算法