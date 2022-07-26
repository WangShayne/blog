---
title: gost 多级代理
date: 2022-07-26 08:47:32
tags:
---

# 前言：
很多朋友在寻找什么样才是最安全的挖矿网络解决方案，那么在此之前，要先了解什么是stratum协议。

stratum协议是目前最常用的矿机和矿池之间的TCP通讯协议，是长连接协议，目前只有在挖矿场景才会用到。

常用的是有stratum+tcp和stratum+SSL两种，有些朋友以为用了stratum+SSL就是安全的，运营商就无法监测得到，这种想法是错误的。

stratum+tcp和stratum+SSL只是内容数据包有没有加密的区别，但是用了SSL只是数据明文变成了加密数据，其外部特征不变的，且该协议特征明显，只有挖矿才会用到，所以通过机器学习、大量数据训练等方法，该协议非常容易就能够被识别出来。

举个例子，就像一辆汽车，TCP就是没有拉上窗帘，外面的人一眼就可以看到车里面的人，SSL就像拉上了窗帘，外面的人是看不到车里的人，但是还是可以一眼就看得出这是一辆汽车，而且这种车型，只有挖矿的数据才会乘坐。

目前，运营商已经在多地部署响应的挖矿流量监控系统，四川，江苏，河南，浙江等地，已经发生多起被监控到而断网的事件。

<br>
<br>


# GOST加密隧道介绍：
GOST是一款用GO语言实现的安全隧道软件，gost安全隧道使用的是 http+tls 的协议，目的是伪装成普通的网页流量访问，让流量特征监控失效。

官网在这里                  https://github.com/ginuerzh/gost
最新版下载在这里      https://github.com/ginuerzh/gost/releases

Gost 隧道需要两端，即服务端与客户端才能组成一条加密隧道，
所以需要一台境外服务器做服务端，同时在你的本地也需要一台客户端（可以是矿机本身，也可以是任意一台能运行 Gost 的机器，包括Docker容器）服务端和客户端互相加密通讯，就能够组成一条加密隧道。

![GOST解密隧道的挖矿网络拓扑图-来自91wa.net](1.png)

<br>
<br>

# 服务端设置

下载解压

```
    wget https://github.com/go-gost/gost/releases/download/v3.0.0-beta.2/gost-linux-amd64-3.0.0-beta.2.gz
    gzip gost-linux-amd64-3.0.0-beta.2.gz -d
    mv gost-linux-amd64-3.0.0-beta.2.gz gost && chmod +x gost
```

启动server端

9443为服务端端口,可以任意设置,客户端必须一致
```
    ./gost -L=relay+tls://:9443
```

### 长运行可以用nohup 或者 tmux

<br>
<br>

# 客户端设置
> 可以部署在矿机或其他机器,能访问到就可以



以linux为例,下载过程与服务端一致

### 命令解析
-L=tcp://:本机挖矿端口/矿池地址:矿池端口 -F=relay+tls://服务端IP:服务端端口

<br>


## 单机
启动
```
    gost -L=tcp://:12345/asia1.pool.org:14444 -F=relay+tls://服务端IP:9443
```
<br>

## 多级

> 矿机>>>GOST加密隧道>>>大陆服务器>>>>GOST加密隧道>>>香港服务器>>>>矿池

在多台中转机器上启动服务端

```
    gost -L=tcp://:12345/asia1.pool.org:14444 -F=relay+tls://大陆服务器IP:9333 -F=relay+tls://香港服务器IP:9443 
```
多级需要显示声明多台服务器的地址,gost将会按照声明的顺序依次将流量传递
原作者:
![2](2.jpg)
![3](3.jpg)