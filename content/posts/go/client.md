---
title: "Go客户端开发"
date: 2021-12-01T14:18:46+08:00
summary: "关于Go客户端开发的实践分享"
tags: ["Web", "Client", "Windows"]
categories: ["Go"]
---

## 细粒度的请求

我在开发一个自动连网的客户端时的请求流程.

### 创建请求和发送请求

```go
// 发送GET请求可以不需要body, 传入nil代表空body.
http.NewRequest("GET",  "https://example.com/hello", nil)
// 可以使用strings.NewReader构建body.
http.NewRequest("POST", "https://example.com/login", strings.NewReader(body))
// 可以使用默认的客户端发送创建的请求
http.DefaultClient.Do(request)
```

### 控制请求头

```go
request.Header.Set("Cookie", cookie)
request.Header.Set("Content-Type", "application/x-www-form-urlencoded")
```

### 注意事项

在`http.XXX`等默认封装的请求中, Content-Type为`http.XXX`的一个参数,

但在自己封装的请求时, 我们需要手动添加Content-Type头部信息, 别忘记了!

## 掉线重连的实现方法

首先我们需要定时侦测连接的状态, 但请求的目标服务器并未包含相关接口.

由于我们场景的特殊性, 只需要检测到任意常用服务器的连通性即可(间接).

当然实现Ping检测是一个非常不错的选择, 但Get常用网站的首页会显得更为容易.

### 超时控制

```go
// 将检测时限控制在非常小的范围内.
context.WithTimeout(context.Background(), time.Second)
http.NewRequestWithContext(ctx, "GET", "https://www.baidu.com", nil)
```

### 后台运行

由于目标平台是Windows, 我们需要实现后台运行.

```shell
go build -ldflags -H=windowsgui .
```

通过改变编译过程, 程序可以后台运行, 但同时控制台输出会被丢弃.

如果你的应用程序确实有很多控制台信息, 尝试编译两个版本吧!
