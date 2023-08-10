---
title: "Docker Network"
date: 2023-03-13T13:57:00+08:00
tags: []
categories: ["Docker"]
summary: "Docker Bridge Driver and Overlay Driver"
---

## Bridge

Docker Bridge Driver底层使用Linux Bridge实现, 具有交换机的功能.

默认的Bridge网络无DNS解析功能, 只有手动创建的Bridge网络才配备有DNS解析功能.

容器访问外网需要借助NAT, 外网访问容器需要端口映射.

## Overlay

Docker Overlay Driver用于连接不同主机中的容器.
