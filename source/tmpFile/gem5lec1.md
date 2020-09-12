***?
---
title: gem5lec1
date: 2020-08-21 21:02:37
tags:
---



安装

```shell
sudo apt install git
sudo apt install scons
sudo apt install python-pip
sudo apt-get install six
```

```shell
sudo apt install build-essential git m4 scons zlib1g zlib1g-dev libprotobuf-dev protobuf-compiler libprotoc-dev libgoogle-perftools-dev python-dev python
```

```shell
mkdir temp
cd temp
git clone https://gem5.googlesource.com/public/gem5
scons build/X86/gem5.opt -j5
```

git clone 的这个网站需要vpn

<img src="gem5lec1/image-20200821211421714.png" alt="image-20200821211421714" style="zoom:50%;" />

#### M4 macro processor not installed

If the M4 macro processor isn’t installed you’ll see an error similar to this:

```
...
Checking for member exclude_host in struct perf_event_attr...yes
Error: Can't find version of M4 macro processor.  Please install M4 and try again.
```

Just installing the M4 macro package may not solve this issue. You may nee to also install all of the `autoconf` tools. On Ubuntu, you can use the following command.

```
sudo apt-get install automake
```



#### gem5 architecture

* gem5 consists of **SimObjects**

most C++ objects in gem5 inherit from `class SimObject`

Represent 



* gem5 is a discrete event simulator



#### gem5 use inteface

