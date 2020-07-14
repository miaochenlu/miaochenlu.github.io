---
title: "Undo+Redo Logging"
tags: HTM hardware
key: page-redoundologging



---

<!--more-->



## Background



#### Software logging VS. Hardware logging

<center><img src="../../../../assets/images/image-20200713191650882.png" alt="image-20200713191650882" style="zoom:80%;" /></center>

* extra instructions in the CPU pipeline

如图(a)所示，software logging需要在程序中写明logging代码，这会被编译成很多的指令，挤占CPU处理资源

* Increased NVRAM traffic

如果是undo logging, 每次生成log都需要从cache中读旧值，增加了memory traffic

* Conservative cache forced write-back

因为log区域是有限的，数据不写回log就不能被改动。因此当log满了之后，software会在事务提交前保守地强制写回cache里面的数据。这样的话，redo log的使用是没有优势的， 因为redo log强调no force, 但是因为log area的限制依然要force。

* risks of persistence in multithreading

上下文切换会影响



#### undo+redo logging VS. undo logging VS. redo logging

<center><img src="../../../../assets/images/image-20200714204149564.png" alt="image-20200714204149564" style="zoom:80%;" /></center>

这里的log全部采用uncacheable的方式

* undo logging在commit之前需要forced cache write-back

* redo logging需要memory barrier

* undo+redo logging可以"steal" and "no-force"。如果log area unlimited, 则可以真正no-force, 但是对于limited log area, 还是需要forced cache write-back





## 主要设计思想与评价

* Hardware undo+redo logging
* Force Write-Back (FWB): 也是通过cache scan工作的，和PiCL类似, 就不解释了

<center><img src="../../../../assets/images/image-20200714134126760.png" alt="image-20200714134126760" style="zoom:80%;" /></center>

<br>

log entry的组成

* 1bit torn bit

* 16bit transaction ID
* 8bit thread ID: 如果是centralized logs的话就需要thread ID记录是属于哪一个thread的log, 如果是distributed log (per-thread log) 的话就不需要了
* 48bit physical address of data
* one word old value
* one word new value

<br>

步骤：

upon updating an L1 cache line (from old value $A_1$ to a new value $A_1'$)

1. 如果是对cache line的第一次修改，就写一个log record header以及new value到MC中的log buffer; 如果不是第一次修改了，就只将new value $A_1'$ 写回log buffer

1. 获取旧值，如果cache hit, 就直接将old value写回log buffer, 如果cache miss, 则还要从lower level cache中取出old value放入L1 cache， 再将old value写回log buffer
2. L1 cache controller将new value写回L1 cache中
3. 然后memory controller会以FIFO的方式将log buffer中的内容evict到NVRAM。这个evict的步骤和其他步骤是独立的
4. 重复上述步骤如果数据A被多次修改。



## 其他文章的评价