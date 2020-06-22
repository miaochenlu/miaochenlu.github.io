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



## 2020-06-22

近几天读了《湖南农民运动考察报告》(一九二七年三月)，里面有一句话挺有意思，"矫枉必须过正，不过正不能矫枉"。我觉得这可能是一个不断振荡的过程。如果要让这个过程收敛，则每次矫枉过正的幅度不能太大。如果太大了，则会发散，导致走向另一个极端。

分享一首诗，作者是一名江苏的高中生

<center>江城子</center>

少年自有少年狂,藐昆仑，笑吕梁。磨剑数年,今日显锋芒。烈火再炼双百日, 化莫邪,利刃断金刚。雏鹰羽丰初翱翔,披惊雷,傲骄阳。狂风当歌,不畏冰雪冷霜。欲上青天揽日月, 倾东海,洗乾坤苍茫。

"理想主义不死, 著眼看乾坤"

<img src="../assets/images/00100EC7C1D6112F3236A26852D17D08.jpg" alt="00100EC7C1D6112F3236A26852D17D08" style="zoom:25%;" />

## 2020-06-11

今天做了算法设计与分析的课堂练习，其实也就是考试啦。感觉还是有点难度的。

第一题是个棋盘问题，截去了左上角和右下角后让我们用$1\times 2$和$2\times 1$的块覆盖，能否完美覆盖？给了提示转换为matching problem。我一看都懵了，这问题和matching有啥关系....最后灵光一闪xixi, 用$1\times 2$或者$2\times 1$覆盖的两个格子就是一对匹配点呀。这是一个二部图，一侧节点个数比另一侧多，不可能完美匹配。最后一个图是个计算两个多边形交点多问题，我暂时认为用plane sweeping algorithm是不可能为$O(n)$的复杂度的，因为用优先队列维护O(n)节点的stopping point data structure是$logn$的复杂度。听jsbdl说用了竞赛里面的算法达到了$O(n)$的复杂度，ym。其他题目我感觉都还好吧，基本上限定了思路。

考完试又要面对大量的ddl, 还有3篇论文，一个大程，2门考试。感觉又考试又论文真是double 了学生了负担，甚至不止double。我感觉论文真是比考试要难。思考了一下，为什么我觉得论文比考试难呢？

> 考试是一个限定了自由度的东西，或者说是一个常数规模的input, 我算法的复杂度是有限的。
>
> 而论文是一个不限制自由度的东西。首先你要限定自由度，对我而言，这个过程非常耗时，甚至比写起来还耗时。限定了自由度之后，内容又是未知的，还得去找，去读各种各样的论文，然后提炼汇总。

总之，加油吧。

## 2020-06-10

今天去演了职业生涯规划的戏，黑历史无疑了😓，以后不吐槽别人没演技了，背台词还挺累的，干脆现场提词，嘻嘻。

最近感觉整个人都很烦躁。ddl太多了，太焦虑，静不下心来是一个原因。然后还有一个原因就是经常被消息打断，老是想水群，聊天，注意力一下就被分散了。hmmmm, 得改改这个习惯，该玩手机就玩个爽，不要身在曹营心在汉。

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
