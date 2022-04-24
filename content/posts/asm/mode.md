---
title: "Intel处理器工作模式"
date: 2022-03-18T14:20:00+08:00
tags: ["CPU"]
categories: ["Assembly"]
summary: "Intel处理器工作模式"
---

## Intel 386

Intel为满足用户多任务的需求, 开发了286系列CPU, 同时引入了**保护模式**; 但没有考虑向后兼容的问题.

Intel为维持兼容性, 开发了386系列CPU, 同时引入了**虚拟8086模式**, 使得以前实模式下的软件能够继续使用.
