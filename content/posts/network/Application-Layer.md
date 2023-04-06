---
title: "Application Layer"
date: 2022-07-10T12:00:00+08:00
categories: ["Network"]
summary: "Application Layer"
---

## HTTP

### 状态码

* 1xx: 提示信息
* 2xx: 成功 
* 3xx: 重定向, 301(Moved Permanently), 302(Found).
* 4xx: 客户端错误, 400(Bad Request), 403(Forbidden), 404(Not Found).
* 5xx: 服务端错误

### HTTP/1.1

* 长连接: 改善了短连接造成的性能开销.

### HTTP/2

* 头部压缩：头信息表.
* 二进制格式：头信息和数据体都是二进制, 并且统称为帧.
* 并发传输：多个Stream复用在一条TCP连接, 缓解了队头阻塞.

### HTTP/3

* 无队头阻塞: Stream之间并没有依赖, 某个Stream发生丢包了, 只会影响该流.

## RPC

传输内容: 可以采用其他协议保存结构体数据, 同时不需要考虑浏览器行为, 性能好.
