---
title: "HOOP"
tags: HTM hardware
key: page-HOOP

---



# HOOP: Efﬁcient Hardware-Assisted Out-of-Place Update for Non-Volatile Memory

<a href="https://jianh.web.engr.illinois.edu/papers/hoop-isca2020.pdf">paper link</a>

> 我个人认为这篇文章提出的方法属于optimized shadow paging。之前我也想到过不用log而用shadow paging的方法，但是显然不如这篇文章完整巧妙，对以往技术都总结的很清楚。

首先，目标要保证non-volatile memory的atomic durability.

所谓atomic durability

> **Atomic durability** guarantees that upon a power failure, either all or none of a transaction's updates are made **durable**

一般实现atomic durability通常使用logging或者采用shadow paging等技术。但是文章认为:

> most of them either introduce **extra write trafﬁc** to NVM or **suffer from signiﬁcant performance overhead on the critical path** of program execution, or even **both**.

因此，这篇文章提出了HOOP

HOOP在NVM中开辟了两块区域: 一块home region放旧值；一块OOP region放新值。

提出garbage collection算法在后台并行将OOP数据写回到home region.

HOOP维护一个hash-based address-mapping table来做physical-to-physical address translation（map from home region addresses to the OOP region addresses) .(指示了该访问home region还是访问OOP region)

我们基本上可以想象出这个系统的naive的流程了

* 对应的项新值写入了OOP region
* 在address-map中加入了home region address, OOP region address的项
* (如果这个时候新值被访问，因为map中存在该项目，因此通过map访问OOP region的该数据地址)
* GC算法将该数据从OOP region写回到home region
* map中删除该项
* (如果这个时候值被访问，因为map中不存在该项目，因此直接访问home region

<br>

### Architecture Overview

<img src="../../../../assets/images/image-20200511233348377.png" alt="image-20200511233348377" style="zoom:50%;" />

... tbc

