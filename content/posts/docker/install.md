---
title: "Docker安装指南"
date: 2022-04-03T14:00:00+08:00
tags: ["Install"]
categories: ["Docker"]
summary: "Docker安装指南"
---

## 相关资料

[Docker官方安装指南](https://docs.docker.com/engine/install/)

## CentOS

### 配置软件源

```
[docker-ce-stable]
name=Docker CE Stable
baseurl=https://download.docker.com/linux/centos/$releasever/$basearch/stable
enabled=1
gpgcheck=1
gpgkey=https://download.docker.com/linux/centos/gpg
```

### 安装命令

```shell
# 更新信息
yum clean all
yum makecache
# 下载安装
yum install docker-ce
# 启动服务
systemctl start docker
```

## Ubuntu

## 配置软件源

```
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: focal
Components: stable
arch: amd64
signed-by: /usr/share/keyrings/docker.gpg
```

### 安装命令

```shell
# 下载并生成密钥
wget -O public.key https://download.docker.com/linux/ubuntu/gpg
sudo gpg --dearmor -o /usr/share/keyrings/docker.gpg public.key
# 更新信息
sudo apt update
# 下载安装
sudo install docker-ce
```

## 用户组

如果希望将用当前用户操作Docker, 需要将当前用户加入到Docker用户组中, 并重新登录.

```shell
sudo usermod --append --groups docker <USER>
```

## 检查安装

```shell
docker version
```