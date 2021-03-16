---
title: 延长分支处理时间
date: 2021-03-16 16:09:29
tags: [Computer Architecture, Research, Random Thought]
categories: Computer Architecture
index_img: /img/think.jpg
---



# problem

最近在做spectre attack时，遇到了一个小问题。这是解决该问题的一种方式的小总结

我们想要延长处理分支(问号部分)的时间，以便能够speculatively execute更多分支中的语句，使得我们的实验结果看起来更显著。

```c
if(x < ???) {}
```

# Initial Thought: naive way

首先想到的是，在分支中放入很多需要从memory中load的数据，来延长时间，用Gem5得到的实验结果如下

```c
time1 = __rdtscp(&junk);
if(x < N[0][0] + N[1][0] + N[2][0] + N[3][0] + N[i][0] + ...) {}
time2 = __rdtscp(&junk) - time1 - overhead;
```

| N                                                 | clock cycles |
| ------------------------------------------------- | ------------ |
| $N[0][0]$                                         | 3            |
| $N[0][0]+N[1][0]$                                 | 136          |
| $N[0][0]+N[1][0]+N[2][0]$                         | 137          |
| $N[0][0]+N[1][0]+N[2][0]+N[3][0]$                 | 136          |
| $N[0][0]+N[1][0]+N[2][0]+N[3][0]+N[4][0]$         | 148          |
| $N[0][0]+N[1][0]+N[2][0]+N[3][0]+N[4][0]+N[5][0]$ | 160          |

实验结果不尽如人意，不断加入N来延长分支处理时间的效果非常有限。事实上，这些load并没有依赖关系，应该可以并行处理，这使得他能够hide memory latency。

# Second Thought: dependence

如果说load的并行执行是一个关键所在，那么使这些load产生依赖关系，打破这种并行便是一个突破点。

比如$N[N[1][0]][0]$, $N[1][0]=0$, 那么首先要知道$N[1][0]$的值，才能进一步load, 这里是$N[0][0]$。这样就把latency拉成一条直线了

```c
for(int i = 1; i < 20; i++)
    N[i][0] = i - 1;

time1 = __rdtscp(&junk);
if(x < N[N[N[N[3][0]][0]][0]][0]) {}
time2 = __rdtscp(&junk) - time1 - overhead;
```

如下实验结果

| N                                       | clock cycles |
| --------------------------------------- | ------------ |
| $N[0][0]$                               | 3            |
| $N[N[1][0]][0]$                         | 142          |
| $N[N[N[2][0]][0]][0]$                   | 151          |
| $N[N[N[N[3][0]][0]][0]][0]$             | 279          |
| $N[N[N[N[N[4][0]][0]][0]][0]][0]$       | 433          |
| $N[N[N[N[N[N[5][0]][0]][0]][0]][0]][0]$ | 575          |

