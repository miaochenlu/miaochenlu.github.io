---
layout: article
title: Probelm Reduction
tags: DAA
key: page-DAA
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
  background_image:
    src: https://miaochenlu.github.io/picture/EDD6F47E7DDE25D08BB6320511A6F0BD.png




---

<!--more-->

# 1. What is problem reduction

Reducing $P_1$ to $P_2$ ($P_2$)

我们想要解决问题 $P_1$， 手上有解决问题$P_2$的算法$A_2$。将问题$P_1$的实例$I_1$变换成问题$P_2$的实例$I_2$。用算法$A_2$解决$I_2$, 得到解$S_2$。再将$S_2$变换成$I_1$的解$S_1$.

<img src="../../../../assets/images/image-20200510093212231.png" alt="image-20200510093212231" style="zoom:50%;" />

这个合成的算法就是问题$P_1$的算法$A_1$

Algorithm *A*1:

* 对于问题$P_1$的实例$I_1$, 生成问题$P_2$的实例$I_2$
* 用算法$A_2$解决问题$P_2$的实例$I_2$
* 得到解$S_2$
* 将$S_2$转换成$I_1$的解$S_1$

合成算法$A_1$可能比较慢(不会比$A_2$快)， $time(A_1)\geq time(A_2)$

Upper bound for $P_1$: $time(A_1)\leq conversion\;time(input\;and \;output) +time(A_2)$



<br>

# 2. limits: If $P_1$ is hard, then so is $P_2$

The general "reducing" algorithm, $P_1\propto P_2$

1. 对于问题$P_1$的实例$I_1$, 生成问题$P_2$的实例$I_2$

2. 用算法$A_2$解决问题$P_2$的实例$I_2$, 得到解$S_2$

3. 将$S_2$转换成$I_1$的解$S_1$

step 1 and 3 need to be dominated by step 2's runtime. 这个时候我们可以说$P_1, P_2$是一样难的

如果可以很容易的解决$P_2$, 那么我们也可以很容易的解决$P_1$

同样，如果解决$P_1$很难，那么解决$P_2$也很难。

做reduction的目的就在于说明$P_2$ hard, 并不是真的要用它去解决问题



## 2.1 Examples

### A. Distinct Integers Sorting

`Theorem`{:.error}

Any algorithm for sorting distinct integers has worst case runtime $\Omega(nlogn)$

Proof:

假设算法$A_{SDI}$可以在$T(n)$时间sort distinct integers($P_2$)

我们可以说明general sorting($P_1$)的算法A时$O(T(n))$的。因为A是$\Omega(nlogn)$的，那么$A_{SDI}$也必须是$\Omega(nlogn)$的

<br>

给定算法$A_{SDI}$

我们想要sort $\{a_1,a_2,\cdots,a_n\}$, 这些数不一定distinct(也就是General Sorting)

对每个$a_i$, 令$b_i=a_in+i$

&emsp;注意到，如果有$a_i<a_j$, 就有$b_i<b_j$

所有的$b_i$都distinct

用$A_{SDI}$来对$b$做sorting

> Claim: if $b_i <b_j$, then $a_i\leq a_j$
>
> Proof:
>
> 如果$b_i<b_j$, 也就是$a_in+i<a_jn+j$
>
> 那么对于$1\leq i,j\leq n$, 有
>
> * $a_i=a_j$ and $i<j$
> * $a_i<a_j$
>
> 这两种情况下都说明$a_i\leq a_j$

所以对$b_i$对排序等价于对$a_i$的排序

<br>

总结一下：

sorting distinct integers这个问题事先不知道他的复杂度。然后我们证明了如果我们可以解决这个问题，我们就可以解决sorting in general的问题, 即

$$General\; Sorting\propto Distinct\; Integer\; Sorting$$

那么我们就知道了sorting distinct integer和sorting in general是一个难度级别的

<br>

### B. Integer Distinctness Problem

判断给定一组整数$\{x_1,x_2,\cdots,x_n\}$, 这些整数是否不同

接下来我们要分析这个问题的复杂度。

如果comparison只有"equal"和"not equal"两个答案，那么复杂度: 

Lower bound $=\Omega(n^2)$ By Adversary

因为任何两个数之间都要比较一下，否则adversary就可以更改结果

如果comparison有$<,=,>$的答案，那么复杂度是多少呢？

我们接下来证明

$$Integer\; distinctness\;problem\propto sorting$$

<br>

给定$\{x_1,x_2,\cdots,x_n\}$

1. Sort N items. 假设$x_i$各不相同，对$x_i$进行排序

$$x_1<x_2<\cdots<x_i<x_{i+1}<\cdots<x_n$$

2. Check adjacent pairs for equality. 接下来要比较所有$x_i,x_{i+1}$。否则的话，假设$x_k,x_{k+1}$没有进行比较，那么在改动$x_k=x_{k+1}$后，该算法仍然会给出distinct的结果

这些比较可以在线性时间完成。

所以最终integer distinctness problem需要$N log N + N$



<br>

### C. Closest Pair Problem

给定n个数$\{x_1,x_2,\cdots,x_n\}$. 找到一对数，他们之间的差距最小

这个问题的复杂度$T(n)=O(nlogn)$

因为$Integer\; distinctness\;problem\propto Closest\;pair\;problem$

稍加思索可知，这个reduction是对的。如果closest pair problem得到最小的difference是0， 那么integer distinctness problem给出的答案就是"no", 如果closest pair problem得到最小的difference不是0， 那么integer distinctness problem给出的答案就是"yes"







# 3. Time Complexity on Reductions

* Linear reductions ($P_1\propto_l P_2$)

Step 1: $O(\vert I_1\vert$)time to pepare $I_2$ for $P_2$

Step 3: $O(\vert I_1\vert) $ time to change $S_2$ into $S_1$ to $I_1$



* Polynomial reductions ($P_1\propto_p P_2$)

Step 1: $O(f(\vert I_1\vert))$ time to pepare $I_2$ for $P_2$

Step 3: $O(f(\vert I_1\vert)) $ time to change $S_2$ into $S_1$ to $I_1$

A reduction from problem $P_1$ to $P_2$ in $O(f (n))$ time and an $O(g(n))$ time algorithm for $P_2$ gives an $O(f(n)+g(n))$ algorithm for $P_1$. 

<br>

# 4. More Problems

### 4.1 TSP and HC

Traveling Salesman Problem (TSP): (optimization problem)

> A salesman aims to visit each of the *n* cities at least once and return to his starting point

Given: A weighted complete graph G

Goal: A minimum TSP tour in G.



Hamiltonian Cycle: (decision problem)

> Given an unweighted graph G=(V,E), does the graph have a tour which visits every vertex exactly once?



### 4.2 Two more graph problems

Max Independent set

> 给定图$G=(V,E)$, 找到最大的集合$S\subseteq V$是的S中的任意一对点之间都没有边连接。

Max-Clique

> 给定图$G=(V,E)$, 找到最大的集合$S\subseteq V$使得S中的任意两个顶点都是邻接的

`Theorem`{:.error}

$Max-Clique\propto_p Max-Iin-Set$

$Max-Ind-Set\propto_p Max-Clique$

定义G'是G的complementary graph

<img src="../../../assets/images/image-20200513130026713.png" alt="image-20200513130026713" style="zoom:50%;" />

可以发现在G中的clique和在G'中的independent set是相同的

<br>

Algorithm:

给定Max-Clique的算法$A_2$, 那么就有Max-Ind-Set的算法

1. 根据G计算complementary graph G'
2. 在G'中计算Max-Clique S
3. 返回S，S也是G的Max-Ind-Set

同理，Max-Clique的算法



### 4.3 3SUM and Collinear

#### A. 3SUM Problem

3SUM

> Given a set S of n distinct integers, are there $a,b,c\in S$ with $a+b+c=0$ ?

Algorithm: $O(n^2)$

1. Sort S in increasing order
2. Consider $x_i,x_j, x_k$, start with $i=1,j=2,k=n$
3. If $x_i+x_j+x_k<0$, then $j=j+1$ else $k=k-1$
4. If no solution $i=i+1$ (and repeat)

<img src="../../../assets/images/image-20200513131348193.png" alt="image-20200513131348193" style="zoom:30%;" />

Example for 3SUM

<img src="../../../assets/images/image-20200513131648712.png" alt="image-20200513131648712" style="zoom:40%;" />



#### B. Collinearity

Collinearity

> Given a set of n points in the plane,  

$3SUM\propto Collinearity$

* 给定3SUM中的集合S, 将S中的整数$a$替换成3个点$(a,0),(-\frac{a}{2},1),(a,2)$
* 那么我们在$y=0,y=1,y=2$这几条horizontal line上总共有3n个点
* 任何一条non-horizontal line一定会和$y=0,y=1,y=2$相交于3个点，比如$(a,0),(\frac{-b}{2},1),(c,2)$
* $slope=-\frac{b}{2}-a=c+\frac{b}{2}$, 因此如果collinear, 则有$a+b+c=0$
* 如果$x,y,z\in S$, $x+y+z=0$, 那么$(x,0),(-\frac{y}{2},1), (z,2)$ are collinear

<img src="../../../assets/images/image-20200513143232468.png" alt="image-20200513143232468" style="zoom:50%;" />

当然这里存在一个问题，如果$x=y$, 那么这三个点就不是collinear的

<br>

复杂度

$$T_{3SUM}(n)\leq T_{collinear}(3n)+O(n)$$

$$T_{collinear}(n)\geq T_{3SUM}(\frac{n}{3})-O(n)$$

<img src="../../../assets/images/image-20200513133711119.png" alt="image-20200513133711119" style="zoom:40%;" />

#### C. Looking Back at 3SUM

> Given a set S of n distinct integers, are there $a,b,c\in S$(***repetition allowed***) with $a+b+c=0$ ?

Algorithm:  $O(n^2)$

double S，S里面的元素复制一份，一共2n个元素

1. Sort S in  increasing order
2. Consider $x_i,x_j, x_k$, start with $i=1,j=2,k=n$
3. If $x_i+x_j+x_k<0$, then $j=j+1$ else $k=k-1$
4. If no solution $i=i+1$ (and repeat)

<img src="../../../assets/images/image-20200513142041162.png" alt="image-20200513142041162" style="zoom:30%;" />



### 4.4 Segment Splitting Problem

Segment Splitting Problem

> 给定一个平面上的line segments的集合，是否存在一条line既不和任何一条line segment相交，又能将这些line segment 分割成两个非空子集

<img src="../../../assets/images/image-20200513142334774.png" alt="image-20200513142334774" style="zoom:50%;" />

$3SUM\propto Collinearity\propto Segment Splitting$

从3SUM变换到Collinearity的点集开始。

点集如何和line segments集合扯上关系呢？

将每个点用horizontal line segments之间的hole来表示

我们可以发现：如果有3个点collinear，就存在直线分离line segments

<img src="../../../assets/images/image-20200513143644153.png" alt="image-20200513143644153" style="zoom:30%;" />

但是如果存在直线分离line segments，要推出3个点collinear就会有问题。

因为我们的分离line segments的直线不一定能精准的经过那3个点。

一个重要的问题是，这个hole应该画多大呢?

应该为最近两点之间的距离那么大

因此，在$O(nlogn)$时间内通过计算closest pair of points之间的距离来计算hole size

除了3n line segments外，我们还要再加上这13个line segments

<img src="../../../assets/images/image-20200513150223284.png" alt="image-20200513150223284" style="zoom:50%;" />



<img src="../../../assets/images/image-20200513150040567.png" alt="image-20200513150040567" style="zoom:50%;" />

复杂度

$$T_{3SUM}(n)\leq T_{split}(3n+13)+O(nlogn)$$

$$T_{split}(n)\geq T_{3SUM}(\frac{n-13}{3})-O(nlogn)$$



Reference

[1] https://www.cs.princeton.edu/~rs/AlgsDS07/23Reductions.pdf