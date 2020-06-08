---
layout: article
titles:
  en      : &EN       Timeline
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  时间线
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  ko      : &KO       
  ko-KR   : *KO
key: page-timeline
---

## <br/>

书生意气，挥斥方遒。就是要豪迈！

## 2020-06-09

今天做了编译原理展示，准备的过程非常痛苦，ddl前找到了一堆bug, 改的过程中觉得自己快要疯了。展示过程我个人觉得还可以吧，虽然老师指出了没有实现语法分析的错误恢复，之后还得再改改。

看数值分析的ODE部分，一看发现又不会了。可能看这些数值分析的内容觉得很散，很繁琐，确实，要把一捆线拧成一股绳就是很难的。在思考的过程中，我发现这些方法的本质都是做泰勒展开。比如Runge-Kutta方法，虽然rk在$t_i, t_{i+1}$中间插了很多点，但是这些中间的点本质上依赖于$t_i$的函数值, 所以我感觉他和在$t_i$处的高阶泰勒展开没有区别。那么为什么要提出这个方法呢？hhh, 终于被我发现了，因为runge-kutta不用算导数，而泰勒展开要不断算高阶导数。我还是第一次见用插值代替求导的, 不愧是runge-kutta。

在复习DAA的reduction部分的时候，我又遇到了那个我一直都疑惑的问题，$P_1\propto P_2$, 为啥能推出lower bound($P_1$) $\leq$ lower bound($P_2$)。我一直以为是lower bound($P_1$) $\geq$ lower bound($P_2$)。问了ljy后发现，$P_1$转换过去的input是$P_2$的special case, 比如所有的NP问题都可以规约到NPC问题。

写完了思想史论文，舒服了，又少一个ddl。但是现在ddl增加的速度大于我完成ddl的速度, yyy。





## 2019-12-14

今天

- [ ] COMNET tranport layer
- [ ] OS 文件系统
- [ ] paper undo+redo log

## 2019-12-12

hmmmm, 时隔很久又回来了，发现还是需要每天一个计划来督促自己。还有28天就要考试了，要加油哦！

今天

- [x] 心理学导论视觉+知觉part 1+语言
- [x] PPL MUA
- [x] COMNET lab part
- [x] OS进程

明天

- [x] COMNET transport layer part
- [x] COMNET lab 
- [x] OS thread
- [ ] paper undo+redo log

## 2019-10-21

- [x] 早起
- [x] LogTM读完
- [x] MUA part1做完 wwwwwwow
- [x] CA 流水线
- [x] TOEFL 两篇听力 vocabulary.com打卡
- [ ] 操作系统复习
- [ ] Socket pthread

明天

- [ ] 早起
- [ ] TOEFL
- [ ] 读LAD 维护ppt
- [ ] 运筹学作业完成
- [ ] 有空把socket多线程写完

<br/>

## 2019-10-20

- [x] Distributed selector part, DAMMP, discussion
- [x] cache coherence  MSI MOSI MOESI
- [x] MUA BasicElement

今天建了一个托福push群，找了一个外国哥哥来做口语指导，希望能够push大家一起进步，都能战胜托福

明天计划

- [ ] LogTM读完
- [ ] 操作系统复习
- [ ] 体系结构复习
- [ ] Socket pthread
- [ ] MUA operation
- [ ] TOEFL 一篇听力
- [ ] 早起

## 2019-10-19

- [x] Distributed selector part
- [x] 凸优化理论
- [x] 读MUA interpreter code and start coding

真菜啊

明天

- [ ] Distributed selector part, DAMMP, discussion
- [ ] LogTM读完
- [ ] 操作系统复习
- [ ] 体系结构复习
- [ ] MUA BasicElement
- [ ] Socket pthread

## 2019-10-18

- [x] logTM part
- [x] OS实验做完
- [x] socket编程client差不多了，server还不能多线程
- [x] 读MUA interpreter part code

今天去西湖玩了，很多事情都没有做完，哎呀，改变一下心情嘛

为plxjj拍照

<center><img src="https://miaochenlu.github.io/picture/FDDE217660AF15F233663342B52FBC7B.png" alt="FDDE217660AF15F233663342B52FBC7B" style="zoom:10%;" /></center>
## 2019-10-17

- [ ] 读完paper: selector; logTM没有读wok
- [x] 运筹学凸优化 stanford[毛概课前]
- [ ] CSAPP chapter3 some 也没看yyy
- [x] leetcode 3题 超额完成
- [x] head first java
- [x] OS linux module
- [x] socket programming some basics

明天计划

- [ ] 读完paper: distributed selector 继续读DAMMP
- [ ] logTM part
- [ ] 运筹学凸优化 lec2
- [ ] OS实验做完
- [ ] socket编程至少可以连接上
- [ ] C++ STL lec3
- [ ] CSAPP chapter3 some
- [ ] Head first java

<br/>

## 2019-10-16

- [x] paper logTM读了abstract,intro; 读了part of DAMMP 明白了actor model[不得不说，这个真的np
- [x] 计网Hamming code
- [x] head first Java
- [x] C++ STL
- [x] CSAPP chapter 2
- [x] 去运动

wow, 效率不错

<a href="http://myan8.web.engr.illinois.edu">Mengjia Yan</a>

<img src="https://miaochenlu.github.io/picture/9C400D0D8117A7B9BE347462A4EC4C70.jpg" alt="9C400D0D8117A7B9BE347462A4EC4C70" style="zoom: 33%;" />

<br/>

明天计划

- [ ] 读完paper: selector; 继续读logTM[下午可看]
- [ ] 运筹学凸优化 stanford[毛概课前]
- [ ] CSAPP chapter3 some[晚10点左右]
- [ ] leetcode 一题[毛概课可做]
- [ ] head first java[毛概课可看]
- [ ] 可能可以做一下OS实验哦



## 2019-10-15

- [x] paper读完
- [x] ppt维护
- [ ] 计网上节课内容复习完
- [x] 运筹学作业还剩一题做了，看了mit视频发现根本没有张国川老师讲的好emmmm
- [x] leetcode 一题， 约瑟夫环相关
- [ ] java没看kkkkkk

靠，今天效率真低下

码住末代皇帝，有空去看

明天计划

- [ ] paper logTM[19:00-21:00]
- [ ] 计网上节课内容复习完[零碎]
- [ ] head first Java[10:00-11:00]
- [ ] C++ STL[12:00-13:00]
- [ ] CSAPP chapter2 some pages[零碎]
- [ ] 明天必须去运动

<br/>

## 2019-10-14

* C++ STL lec2
* head first java chapter6 [still not quite understand
* paper 《Steal but No Force: Efficient Hardware Undo+Redo Logging for Persistent Memory Systems》 more than half
* CSAPP chapter1 finish

今天flag没倒，其实效率也不高

明天计划

- [ ] paper读完
- [ ] ppt维护
- [ ] 计网上节课内容复习完
- [ ] 运筹学作业还剩一题做了

## 2019-10-13

一觉睡到了10点......[🐷啊你]

主要前几天都熬夜了，准备CA pre 1551

今天

* OS lab1做了一天了，第一次做磁盘爆炸了
* leetcode 832 1207 然后发现c++ STL很不熟悉，还是系统地去学习一下比较好。嗯[确信] bilibili 发现侯捷老师有课，收藏不退出开始学习
* C++ STL lec1
* CA pipeline p455-468
* COMNET Datalinklayer学到Hamming code
* head first java chapter5
* 小小地看了一会head first 设计模式

给自己立个小flag啦

冲冲冲



<center><img src="https:///miaochenlu.github.io/picture/A1BB6411086BB318D116285299E767C4.png" alt="A1BB6411086BB318D116285299E767C4" style="zoom:10%;" /><center>

<center><img src="https://miaochenlu.github.io/picture/292EF9ECEC03431D707514F77CBEA7B9.png" alt="292EF9ECEC03431D707514F77CBEA7B9" style="zoom:50%;" /></center>
