# 并发、并行、分布式

* 并发：一个程序的多个任务同时执行

* 并行：一个任务分解为多个子任务同时执行，协作完成一个问题

* 分布式：并行的计算在不同的计算机上进行



## Computation Graphs

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200114211445345.png" alt="image-20200114211445345" style="zoom: 33%;" />

* `WORD(G)`=sum of execution times of all nodes
* `SPAN(G)`=length of the longest path in G
* ideal parallelism=WORD(G)/SPAN(G)



### 加速比

<img src="/Users/jones/Desktop/miaochenlu.github.io/assets/images/image-20200114215004817.png" alt="image-20200114215004817" style="zoom:33%;" />

`speedup<=WORK/SPAN`

`q=fraction of sequential work`

`speedup<=1/q`

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200114215110176.png" alt="image-20200114215110176" style="zoom:50%;" />

