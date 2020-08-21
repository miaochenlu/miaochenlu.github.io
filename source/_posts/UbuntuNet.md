---
title: VMware虚拟机共享本机vpn
date: 2020-08-21 20:32:46
tags: Ubuntu VPN
index_img: /img/image-20200821203833106.png
---

* 设置windows上的vpn为允许其他设备接入

<img src="image-20200821203437531.png" alt="image-20200821203437531" style="zoom: 80%;" />

* 找到windows的IPv4地址。打开cmd, 敲下命令

```shell
ipconfig
```

<img src="image-20200821203549431.png" alt="image-20200821203549431" style="zoom:80%;" />

* 将虚拟机的网络更改到桥接NAT模式

<img src="826F8F12DC215AB8D882501214537913.png" alt="826F8F12DC215AB8D882501214537913" style="zoom:80%;" />

* 打开ubuntu的网络设置

<img src="image-20200821203806669.png" alt="image-20200821203806669" style="zoom:67%;" />

* 设置代理服务器

<img src="image-20200821203833106.png" alt="image-20200821203833106" style="zoom:80%;" />

这时浏览器已经可以上网，但是命令行还不行，还需要另外设置。

* open ubuntu terminal，输入如下内容

```shell
export http_proxy=...
export https_proxy=...
...
```

<img src="image-20200821203938024.png" alt="image-20200821203938024" style="zoom:80%;" />

* 最后，可以使用vpn🌶️

![image-20200821204028331](image-20200821204028331.png)