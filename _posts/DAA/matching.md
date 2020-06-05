# 1. Matching

Input: 无向图$G=(V,E)$

$M\subseteq E$，如果每个node只在M中的一条边出现，那么$M$就是一个匹配

最大匹配：cardinality最大的匹配

<img src="../../assets/images/image-20200530222955828.png" alt="image-20200530222955828" style="zoom:30%;" />

# 2. Bipartite Matching

Input: 无向，二部图$G=(L\cup R, E)$

$M\subseteq E$，如果每个node只在M中的一条边出现，那么$M$就是一个匹配

最大匹配：cardinality最大的匹配

<img src="../../assets/images/image-20200530223009751.png" alt="image-20200530223009751" style="zoom:30%;" />



### A. Bipartite Matching and Max Flow

创建一个新图$G'=(L\cup R\cup \{s,t\}, E')$



<img src="../../assets/images/image-20200530223144342.png" alt="image-20200530223144342" style="zoom:50%;" />

Max cardinality matching G = Value of max flow G'.

<img src="../../assets/images/image-20200530223416722.png" alt="image-20200530223416722" style="zoom:50%;" />



### Alternating Path Approach

<img src="../../assets/images/image-20200530224529292.png" alt="image-20200530224529292" style="zoom:50%;" />

{:.warning}

An alternating path is a path from an unmatched $v\in V$ to another unmatched $u\in U$ with edges alternatively in E-M and M

<img src="../../assets/images/image-20200530224649637.png" alt="image-20200530224649637" style="zoom: 33%;" /><img src="../../assets/images/image-20200530224710620.png" alt="image-20200530224710620" style="zoom:30%;" />

* Number of unmatched edges = 1 + number of matched edges
* Replace all edges in M by edges in E-M

<img src="../../assets/images/image-20200530224909750.png" alt="image-20200530224909750" style="zoom:33%;" />

* A match is maximum iff it has no alternating paths



### Simple Network

Every arc capacity is one. 

* Every node has either: (i) at most one incoming arc, or (ii) at most one outgoing arc.



<img src="../../assets/images/image-20200530225014203.png" alt="image-20200530225014203" style="zoom:20%;" />

(1) It is a max flow algorithm on the simple network;
(2) With the definition of $V_i$, the edges from $V_{i-1}$ to $V_i$ form an s-t cut whose capacity is at most $|V_i|$ (as a simple network, each vertex of $V_i$ can handle only one unit of flow). It gives that $|V_i|>=|f|$. Summing up the inequality from 1 to l shows that the s-t distance l is no more than $|V|/|f|$;
(3) If |f| is at most $|V|^{1/2}$ , our claim is true as each stage will increase the flow value by at least one, that bounds the num of stages;
(4) If $|f|$ is larger, consider the stages into two phases based on the flow value. In the first phase, the value increases from zero to $|f|-|V|^{1/2}$, while in the 2nd phase, the value goes to $|f|$ (and stops). Clearly, the 2nd phase will not take more than $|V|^{1/2}$ stages. For the first phase, note that the max flow value in the simple networks is larger than $|V|^{1/2}$ , then the s-t distance is bounded from above by  $|V|^{1/2}=|V|/ |V|^{1/2}$. As in the algorithm the s-t distance increases at each stage, the number of stages is thus at most $|V|^{1/2}$ as well.





## Perfect Matching

$M\subseteq E$被称为完美匹配如果每个节点在M的边中出现一次

那对于一个二分图来说，什么时候会有完美匹配呢？



### Marriage Theorem

令S=a subset of nodes

N(S)=the set of nodes adjacent to nodes in S

<br>

二分图$G=(L\cup R, E)$ with $\vert L\vert=\vert R\vert$ has a perfect matching iff

$\vert N(S)\vert\geq \vert S\vert$ for all subsets $S\subseteq L$



<img src="../../assets/images/image-20200530230055339.png" alt="image-20200530230055339" style="zoom:33%;" />



证明

* $\Rightarrow$ 显然

<img src="../../assets/images/image-20200530230315270.png" alt="image-20200530230315270" style="zoom:33%;" />

* $\Leftarrow$

假设G没有完美匹配

将问题转换为max flow porblem并且令$(A,B)$是G'的min cut

By max-flow min-cut, $cap(A,B)<\vert L\vert$

<img src="../../assets/images/image-20200530232545752.png" alt="image-20200530232545752" style="zoom:33%;" />

<img src="../../assets/images/image-20200530232218969.png" alt="image-20200530232218969" style="zoom:33%;" />





## Assignment Problem

给定一个Weighted Bipartite graph, 找到minimum weighted perfect matching

<img src="../../assets/images/image-20200531173904500.png" alt="image-20200531173904500" style="zoom:33%;" />

例子：

n jobs for n workers

Each worker asks different salaries for different jobs

Each worker can only do one job

Q: How to assign jobs to workers with minimum total cost?

<img src="../../assets/images/image-20200531174924985.png" alt="image-20200531174924985" style="zoom:40%;" />



### fact 1

**Adding or subtracting a constant $k$ to any row or column will not affect the optimal assignment (i.e. with the lowest total cost, TC)**

<img src="../../assets/images/image-20200531175404537.png" alt="image-20200531175404537" style="zoom:50%;" />

* cost of pink assignment = 73
* cost of blue assignment = 59 (optimal assignment)

在一行或者一列上加了k后，total cost都会增加k



### fact 2

**After ssubtracting or adding values to rows or columns, and keeping that all entries are $\geq 0$** 

<img src="../../assets/images/image-20200531175855894.png" alt="image-20200531175855894" style="zoom:50%;" />





## Hungarian Algorithm

#### Main Ideas

* Generate successive matrices, equqivalent to the original matrix (Fact 1)
* Each successive matrix is supposed to have more 0s until there is at least one 0 per row and column(Fact 2). 但是这个条件不够(这些0不能在同一行同一列)

#### Steps

* Step1: Subtract the minumum cost Mr in each row

<img src="../../assets/images/image-20200531180424598.png" alt="image-20200531180424598" style="zoom:50%;" />

Mr(左图蓝色) is the **least pay** to each worker

Resultant matrix shows "excess" costs for each worker.



* Step2: Subtract the minumum cost in each column

<img src="../../assets/images/image-20200531180639626.png" alt="image-20200531180639626" style="zoom:50%;" />

* Step3: Cover all 0s with minimum number of horizontal and vertical lines

<img src="../../assets/images/image-20200531180827170.png" alt="image-20200531180827170" style="zoom:50%;" />

* Step4: Create more 0s by using the smallest **uncovered excess cost**

因此：Identify the smallest uncovered excess cost, subtract that cost from all elements in its row and update Mr

<img src="../../assets/images/image-20200531181134690.png" alt="image-20200531181134690" style="zoom:50%;" />

Element $(W2,J3)$ is negative

To have no negative numbers, 2 is added to column J3 and update Mc

<img src="../../assets/images/image-20200531181535210.png" alt="image-20200531181535210" style="zoom:50%;" />

2 is subtracted from rows $W1,W4$ to recover the two 0s (all values are now positive)

<img src="../../assets/images/image-20200531182011677.png" alt="image-20200531182011677" style="zoom:50%;" />



* Step5: Go to step3 to cover all 0s. As only 3 0-celled assignments are possible, step4 is repeated again

Step4 make more 0s

<img src="../../assets/images/image-20200531182134889.png" alt="image-20200531182134889" style="zoom:50%;" />



<br>

<img src="../../assets/images/image-20200531182234445.png" alt="image-20200531182234445" style="zoom:50%;" />

