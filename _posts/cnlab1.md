![image-20190928215709450](/Users/jones/Library/Application Support/typora-user-images/image-20190928215709450.png)

  <br/>

#### <center>本科实验报告 </center>

  <br/>

 <br/>

| 课程名称：   | 计算机网络基础                        |
| ------------ | ------------------------------------- |
| 实验项目名称 | WireShark软件初探和常见网络命令的使用 |
| 姓    名：   | 缪晨露                                |
| 学    院：   | 计算机学院                            |
| 专    业：   | 求计1701                              |
| 学    号：   | 3170102668                            |
| 指导教师：   | 高艺                                  |

 <br/>

 <br/>

 <br/>

 

<center>2019年9月28日</center>


<div style="page-break-after: always;"></div>






# 1 实验目的和要求

- 初步了解WireShark软件的界面和功能

-  熟悉各类常用网络命令的使用



# 2 实验内容和原理

-  Wireshark是PC上使用最广泛的免费抓包工具，可以分析大多数常见的协议数据包。有Windows版本、Linux版本和Mac版本，可以免费从网上下载

- 初步掌握网络协议分析软件Wireshark的使用，学会配置过滤器

- 根据要求配置Wireshark，捕获某一类协议的数据包

-  在PC机上熟悉常用网络命令的功能和用法: `Ping.exe，Netstat.exe,  Telnet.exe, Tracert.exe, Arp.exe, Ipconfig.exe, Net.exe, Route.exe, Nslookup.exe`

- 利用WireShark软件捕捉上述部分命令产生的数据包



# 3 主要仪器设备

- 联网的PC机

- WireShark协议分析软件



# 4 操作方法与实验步骤

- 安装网络包捕获软件Wireshark

- 配置网络包捕获软件，捕获所有机器的数据包

- 配置网络包捕获软件，只捕获特定类型的包

- 在Windows命令行方式下，执行适当的命令，完成以下功能(请以管理员身份打开命令行)：

  1. 测试到特定地址的联通性、数据包延迟时间

  2. 显示本机的网卡物理地址、IP地址
  3. 3.显示本机的默认网关地址、DNS服务器地址
  4. 显示本机记录的局域网内其它机器IP地址与其物理地址的对照表
  5. 显示从本机到达一个特定地址的路由
  6. 显示某一个域名的IP地址
  7. 显示已经与本机建立TCP连接的端口、IP地址、连接状态等信息
  8. 显示本机的路由表信息，并手工添加一个路由
  9. 显示本机的网络映射连接
  10.  显示局域网内某台机器的共享资源
  11.  使用telnet连接WEB服务器的端口，输入（<cr>表示回车）获得该网站的主页内容：

```
GET / HTTP/1.1<cr>
Host: 任意字符串<cr>
<cr>
```

- 利用WireShark实时观察在执行上述命令时，哪些命令会额外产生数据包，并记录这些数据包的种类。



# 五 实验数据记录和处理

- ###  运行**Wireshark**软件，界面是由哪几个部分构成？各有什么作用？

**Wireshark**整个界面如下所示

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917082923147.png" alt="image-20190917082923147" style="zoom:50%;" />

1. **菜单栏**

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917083112852.png" alt="image-20190917083112852" style="zoom:50%;" />

2. **主工具栏**

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917083157630.png" alt="image-20190917083157630" style="zoom:50%;" />

3. **Filter toolbar**

![image-20190917083402876](/Users/jones/Library/Application Support/typora-user-images/image-20190917083402876.png)

4. **Packet List**

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917083544866.png" alt="image-20190917083544866" style="zoom:50%;" />

5. **Packet Detail**

显示的是在**Packet List**中选择的包的详情

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917083657201.png" alt="image-20190917083657201" style="zoom:50%;" />



6. **Packet bytes**

显示的是在**Packet List**中选择的包的数据

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917083801869.png" alt="image-20190917083801869" style="zoom:50%;" />

7. 状态栏

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084309992.png" alt="image-20190917084309992" style="zoom:50%;" />

- ### **开始捕获网络数据包，你看到了什么？有哪些协议？**

看到了**MDNS,ARP,IGMPv3,ICMPv6,LLMNR,DCHP,IGMPv2,SSDP,NBNS,TCP,UDP**

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084455495.png" alt="image-20190917084455495" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084549382.png" alt="image-20190917084549382" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084618089.png" alt="image-20190917084618089" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084736066.png" alt="image-20190917084736066" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084755654.png" alt="image-20190917084755654" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084827912.png" alt="image-20190917084827912" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084846599.png" alt="image-20190917084846599" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917084956082.png" alt="image-20190917084956082" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917085022030.png" alt="image-20190917085022030" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917085043105.png" alt="image-20190917085043105" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917085127553.png" alt="image-20190917085127553" style="zoom:50%;" />

- ### 配置应用显示过滤器，让界面只显示某一协议类型的数据包

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917090011125.png" alt="image-20190917090011125" style="zoom:50%;" />

只显示**tcp**协议类型的数据包

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917085839867.png" alt="image-20190917085839867" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917085810689.png" alt="image-20190917085810689" style="zoom:50%;" />



- ### 配置捕获过滤器，只捕获某类协议的数据包

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20190917090855030.png" alt="image-20190917090855030" style="zoom:50%;" />

<img src="/Users/jones/Desktop/屏幕快照 2019-09-17 上午9.46.39.png" style="zoom:50%;" />

- ### 利用 **Ping.exe**，**Netstat.exe, Telnet.exe, Tracert.exe, Arp.exe, Ipconfig.exe, Net.exe, Route.exe** 命令完成在实验步骤中列举的 **11** 个功能



1. 测试到特定地址的联通性、数据包延迟时间

`ping`命令如下所示

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/68E9AA7BCDACF3902AEA331ADCB751D3.png" alt="68E9AA7BCDACF3902AEA331ADCB751D3" style="zoom:67%;" />

我们来进行测试

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/A81E11FFE2310D31F54B5831203528CB.png" alt="A81E11FFE2310D31F54B5831203528CB" style="zoom: 67%;" />

2. 显示本机的网卡物理地址、IP地址

终端输入`ifconfig /all`

<img src="/Users/jones/Downloads/QQ20190918-0.png" alt="QQ20190918-0" style="zoom:80%;" />

3. 显示本机的默认网关地址、DNS服务器地址

<img src="/Users/jones/Downloads/QQ20190918-0.png" alt="QQ20190918-0" style="zoom:80%;" />



4. 显示本机记录的局域网内其它机器 IP 地址与其物理地址的对照表

终端输入 `arp -a`

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/333F2569F24E06D8DC10D07A79384D8A.png" alt="333F2569F24E06D8DC10D07A79384D8A" style="zoom:80%;" />

5. 显示从本机到达一个特定地址的路由

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/42A4E4F7F1F99A7F253D8851E9895060.png" alt="42A4E4F7F1F99A7F253D8851E9895060" style="zoom: 67%;" />



6. 显示某一个域名的 IP 地址

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/74F664DE4D38AD0A347EADE9978E66FB.png" alt="74F664DE4D38AD0A347EADE9978E66FB" style="zoom:80%;" />

可以看到www.bilibili.com的域名是`120.92.141.155`



7. 显示已经与本机建立 TCP 连接的端口、IP 地址、连接状态等信息

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/1367BEE297CC010CBC23C23FECF527C2.png" alt="1367BEE297CC010CBC23C23FECF527C2" style="zoom:67%;" />

8. 显示本机的路由表信息，并手工添加一个路由

本地路由信息如下所示：

![5844E9F391AC7488AF098C7F3CCDDEDC](/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/5844E9F391AC7488AF098C7F3CCDDEDC.png)

添加一个路由：



9. 显示本机的网络映射连接

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/CFFF62D7F7DB36B0470A3C24A7AF1909.png" alt="CFFF62D7F7DB36B0470A3C24A7AF1909" style="zoom:80%;" />

10. 显示局域网内某台机器的共享资源

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/01DDAC9534C603DE7AB4E0F5BB40A406.png" alt="01DDAC9534C603DE7AB4E0F5BB40A406" style="zoom:80%;" />

11. 使用 telnet 连接 WEB 服务器的端口，输入(<cr>表示回车)获得该网站的主

    页内容:

    GET / HTTP/1.1<cr> 

    Host: 任意字符串<cr> 

    <cr>

输入`telnet md.aclickall.com`

然后发送空白界面，输入`ctrl+']'`,再回车

输入`GET / HTTP/1.1<cr> `

得到部分网页内容如下所示：

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/9591CD1B4DAE713A93A23EDF1A3630C3.jpg" alt="9591CD1B4DAE713A93A23EDF1A3630C3" style="zoom:75%;" />



- ## 观察使用**ping**命令时在**WireShark**中出现的数据包并捕获。这是什么协议?

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/3EBC773B0E323BCC80A6124A7736C69F.png" alt="3EBC773B0E323BCC80A6124A7736C69F" style="zoom:80%;" />

![40686646EE50FA096D9C610B08EC93B6](/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/40686646EE50FA096D9C610B08EC93B6.png)

这是**ICMP**协议

- ##  观察使用**Tracert**命令时在WireShark中出现的数据包并捕获。这是什么协议？

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/0159E4CDC9BAEE921F789EB00A7CDCC3.png" alt="0159E4CDC9BAEE921F789EB00A7CDCC3" style="zoom:80%;" />

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/DF660A2BE74D84E81ACE5628D0842EE4.png" alt="DF660A2BE74D84E81ACE5628D0842EE4" style="zoom:80%;" />

 这是**ICMP**协议



- ## 观察使用**Nslookup**命令时在WireShark中出现的数据包并捕获。这是什么协议？

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/CA8BE9F6474068981076E0E446A55CE4.png" alt="CA8BE9F6474068981076E0E446A55CE4" style="zoom:80%;" />

![F37D92993E229607F7B296E678780773](/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/F37D92993E229607F7B296E678780773.png)

这是**DNS**协议

- ## 观察使用**Telnet**命令时在**WireShark**中出现的数据包并捕获。这是什么协议?

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/9591CD1B4DAE713A93A23EDF1A3630C3.jpg" alt="9591CD1B4DAE713A93A23EDF1A3630C3" style="zoom:80%;" />

<img src="/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/A2C665943E6634858F9D52488578F5CD.png" alt="A2C665943E6634858F9D52488578F5CD" style="zoom:80%;" />

![170EC1CF5F69A1C3E0978419940941A2](/Users/jones/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/170EC1CF5F69A1C3E0978419940941A2.png)

是**TCP**协议

# 六 实验结果分析

- ## WireShark的两种过滤器有什么不同？

Display filter:

> 显示过滤器是在捕获数据包后，显示符合过滤规则的数据包

Capture filter:

> 捕获过滤器是在捕获的时候只捕获符合过滤规则的数据包

- ## 哪些网络命令会在WireShark中产生数据包，为什么？

  - **ping**(ICMP)
  - **Tracert**(ICMP)
  - **Nslookup**(DNS)
  - **Telnet**(TCP)

  因为这些命令会产生数据交换，所以会产生数据包



- ## Ping发送的是什么类型的协议数据包？什么时候会出现ARP消息？Ping一个域名和Ping一个IP地址出现的数据包有什么不同？

ping发送的是`ICMP`协议的数据包

ARP消息：如果端系统A想要同局域网上的某个端系统B发送报文，就先在其ARP高速缓存中查看有无B的IP地址。如果有，就在ARP高速缓存中查找硬件地址，再把这个硬件地址写入MAC帧里，然后通过局域网把MAC帧发往此硬件地址。

域名与IP并不一一对应，一个域名可能对硬件多个ip, ping ip得到的是该ip下的数据包，ping域名可能会得到多ip下的数据包。



# 七 讨论、心得

**IP地址是什么**：IP地址是用来标识网络中的一个主机的，具有唯一性。IP地址=网络地址+主机地址，他是32位二进制数据,通常用十进制表示，用'.'分隔。如：`120.92.141.155`

**DNS是什么**：DNS(Domain Name Server),是用来将域名翻译成IP地址的。如：www.bilibili.com对应`120.92.141.155`



***ICMP***协议是*“Internet Control Message Ptotocol”*（*因特网*控制消息协议）的缩写。它是*TCP/IP*协议族的一个子协议，用于在*IP*主机、路由器之间传递控制消息。

