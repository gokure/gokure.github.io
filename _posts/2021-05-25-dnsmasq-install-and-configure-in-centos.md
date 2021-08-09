---
layout: post
title: CentOS 安装以及配置 DNSmasq
date: 2021-05-25 00:00:00 +08:00
category: blog
tags:  [dnsmasq, linux]
---

## 安装 DNSmasq

```bash
sudo yum -y install dnsmasq
sudo systemctl enable dnsmasq
```

## 配置 DNSmasq

DNSmasq 配置文件为 `/etc/dnsmasq.conf`

首先备份原文件

```bash
sudo cp /etc/dnsmasq.conf /etc/dnsmasq.orig
```

我们需要修改几个参数，分别为：

- `resolv-file=/etc/resolv.dnsmasq.conf` 这个参数表示 dnsmasq 会从这个指定的文件中寻找上游 `DNS` 服务器
- 取消注释的 `strict-order`，表示严格按照 `resolv-file` 文件中的顺序从上到下进行 `DNS` 解析, 直到第一个成功解析成功为止
- 注释掉 `no-hosts`, 默认情况下这是注释掉的, dnsmasq 会首先寻找本地的 `/etc/hosts` 文件再去寻找缓存下来的域名, 最后去上游 `DNS` 服务器寻找。
- 监听指定的网络接口 `interface=eth0`
- 设置 `listen-address=127.0.0.1,0.0.0.0`, 如果只想在内网启动，将 `0.0.0.0` 改成服务器内网 IP
- 禁止读取 `/etc/hosts` 文件 `no-hosts`
- 启用 DSN 缓存 `cache-size=500`
- 加载其他文件 `conf-dir=/etc/dnsmasq.d`

我们来整理下上面我们修改了那些配置内容

需要新建一个 `/etc/resolv.dnsmasq.conf` 文件，这个是配置上游 DNS，也就是真正的公共 DNS

```bash
sudo vim /etc/resolv.dnsmasq.conf
```

```conf
# /etc/resolv.dnsmasq.conf
nameserver 10.3.248.211    # 内网DNS
nameserver 119.29.29.29    # 腾讯DNS
nameserver 223.5.5.5       # 阿里DNS
nameserver 114.114.114.114 # 114 DNS
```

修改 `/etc/dnsmasq.conf`

```conf
# /etc/dnsmasq.conf

# 上游 DNS 路径
resolv-file=/etc/resolv.dnsmasq.conf

# 取消 strict-order 注释
strict-order

# 设置域名解析
address=/devel/127.0.0.1

# 设置监听的网卡
interface=em1

# 监听地址 0.0.0.0 对所有网络有效
listen-address=0.0.0.0

# 禁止读取 /etc/hosts
no-hosts

# 设置 cache size
cache-size=500

conf-dir=/etc/dnsmasq.d
```

添加 `127.0.0.1` 到 `/etc/resolv.conf`

通过 `NetworkManager` 更新 `/etc/resolv.conf`

```conf
# sudo vim /etc/sysconfig/network-scripts/ifcfg-em1
...
DNS1=127.0.0.1
DNS2=119.29.29.29
```

然后重启 `network` 服务

```bash
sudo systemctl restart network.service
```

如果没有使用 `NetworkManager` 维护，可以手动修改 `/etc/resolv.conf`

```bash
sudo vim /etc/resolv.conf
```

```conf
search gw.100tal.com
nameserver 127.0.0.1
nameserver 119.29.29.29
options timeout:1
```

## 启动服务

```bash
sudo systemctl start dnsmasq   # 启动服务
sudo systemctl sotp dnsmasq    # 停止服务
sudo systemctl restart dnsmasq # 重启服务
```

## 设置防火墙

DNSmasq默认使用 53 端口，udp 协议

```bash
sudo firewall-cmd --zone=public --add-port=53/tcp --permanent
sudo firewall-cmd --zone=public --add-port=53/udp --permanent
sudo systemctl restart firewalld.service
sudo firewall-cmd --list-all
```

## 检测

使用 `dig` 工具

```bash
dig your.customhost.local
```

使用 nsloookup 工具

安装 nslookup 工具

```bash
sudo yum install bind-utils
```
