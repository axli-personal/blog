---
title: "Docker镜像"
date: 2022-04-04T17:00:00+08:00
tags: ["Command"]
categories: ["Docker"]
summary: "Docker镜像"
---

## 搜索镜像

```shell
docker search --filter stars=1000 ubuntu
```

## 下载镜像

```shell
docker image pull ubuntu # 默认下载标签为latest的镜像.
docker image pull ubuntu:latest # 指定要下载标签.
docker image pull amazon/amazon-ecs-agent # 下载非官方镜像.
docker image pull gcr.io/cadvisor/cadvisor # 从第三方仓库下载.
```

## 列出镜像

```shell
docker image ls
```

## 查看信息

```shell
docker image inspect ubuntu:latest # 显示镜像细节.
```

## 镜像摘要

镜像摘要是镜像内容的散列值, 它可以用来唯一区分一个镜像; 使用inspect命令可以看到容器的摘要值.

## 删除镜像

```shell
docker image rm ubuntu:latest
```