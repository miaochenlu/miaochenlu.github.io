---
title: "Lazy persistency"
tags: HTM
key: page-lp
---

# Lazy Persistency: a High-Performing and Write-Efficient Software Persistency Technique

# 1. overview

NVM->require failure-safety->eagerly flush data out of caches->large overhead

To reduce overheads, we propose Lazy Persitency(LP)

What is LP?

> A software persistency technique
>
> Allow caches to slowly send dirty blocks to the MVMM through natural evictions.



# 2. Introduction

### 2.1 issue

We now have Non-Volatile Main Memory in the system. But achieving persistency asks for failure-safety.

Typically, the system provides a persistency model consisting of 

> * durability ordering
>
> specifies at which point(or order) stores reach the non-volatile domain in the NVMM
>
> * atomic durability
>
> Specifies which group of stores are to be made durable atomically.



### 2.2 persistency models

#### 2.2.1 Eager persistency

persist barrier

>  Force stores to be (eagerly) flushed out of the LLC to NVMM.



Eager persistency is costly

> * the extra instructions added to the program  to define durability ordering increase the instruction count substantially.
> * these instructions are long latency and cause frequent pipeline stalls.
> * By forcing writes to go to NVMM, the NVMM write endurance is reduced
> * It goes against the principle of make the common case fast



#### 2.2.2 Our method

we let caches slowly send written blocks to the NVMM through natural evictions. 

<u>feature</u>

> * there are no additional writes to NVMM
> * no decreased write endurance
> * no performance problems associated with cache line flushes and durable barriers.

<u>Procedure</u>

> A persistency failure, which occurs when a computation’s result has not been made durable, is discovered using software error detection (checksum).
>
> the persistent checksum in memory is inconsistent with the persistent data it protects.
>
> The system recovers by recomputing the inconsistent results.

<br/>

<u>requirements</u>

> 1. define the persistency region granularity, which is the unit of recovery
> 2. a persistency failure detection mechanism needs to be embedded into the code
> 3. The recovery code corresponding to the persistency granularity is needed

<center><img src="https://miaochenlu.github.io/picture/image-20191005205356092.png" alt="image-20191005205356092" style="zoom:80%;" /></center>
<br/>

# 3. background

### A. Intel Persistency Programming Support

compare eager persistency (intel PMEM as example) with lazy persistency

<u>Requirements:</u>

> * Explicitly flush a cache block written to a store instruction in order to force the store to become durable
> * A store fence is needed afterward to avoid stores that follow it(younger stores) from becoming durable before stores that precede it(older stores)

we can see that, a store fence has 2 roles:

1. Acts as memory barrier that ensures older stores are visible to other threads

2. Acts as a durable barrier that ensures older stores(including cache line flush or write back instructions) are durable prior to younger stores.

   > this is supported by Asychronous DRAM Refresh(ADR) platform. It requires the write buffer in the memory controller(MC) to be in the non-volatile domain.
   >
   > When a dirty block is flushed out of the cache hierarchy into the MC, it can be considered durable.



```cpp
//Illustration of a durable transaction with Eager Persistency using PMEM.
for(i = 0; i < N; i++) {
  //log create
  logC = createLog(&C[i], C[i]);
  logD = createLog(&D[i], D[i]);
 	logLast_i = last_i;
  
  CLFLUSHOPT(logC);
  CLFLUSHOPT(logD);
  CLFLUSHOPT(logLast_i);
  SFENCE;
  
  //logStatus1
  logStatus = 1;
  CLFLUSHOPT(&logStatus);
  SFENCE;
  
  //modify data
  C[i] = foo(A[i], B[i]);
  D[i] = bar(A[i], B[i]);
  last_i = i;
  CLFLUSHOPT(&C[i]);
  CLFLUSHOPT(&D[i]);
  CLFLUSHOPT(&last_i);
  SFENCE;
  
  //logStatus2
  logStatus = 0;
  CLFLUSHOPT(&logStatus);
  SFENCE;
}
```

from above codes, we can see that

> It assumes that each loop iteration forms a durable transaction
>
> 在log create和logStatus1中间插入内存屏障，使得log create在logStatus1设置之前完成。
>
> 在logStatus1和modify data之间插入内存屏障，使得在实际数据修改之前logStatus已经durable了
>
> 在modify data和logStatus2之间插入内存屏障，使得数据修改全部完成之后再修改log状态
>
> 



```cpp
for(kk = 0; kk < n; kk += bsize) {
  for(ii = 0; ii < n; ii += bsize) {
    for(jj = 0; jj < n; jj += bsize) {
      //block内乘法
      for(i = ii; i < (ii + bsize); i++)
        for(j = jj; j < (jj + bsize); j++) {
          sum = c[i][j];
          for(k = kk; k < (kk + bsize); k++)
            sum += a[i][k] * b[k][j];
          c[i][j] = sum;
        }
    }
  }
}
```

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191007221647476.png" alt="image-20191007221647476" style="zoom:50%;" />

## 4 Lazy Persistency

### A. Definition

LP require programmer organize their algorithm into <u>LP regions</u>. Each LP region is a unit of recovery.手动将算法分成LP区域，每个LP区域是一个recovery单元



怎么判断在一个LP region中的所有数据都在failure之前变成durable了呢？

背景是：

>  Since Lazy Persistency relies on <u>natural cache evictions</u> to lazily write back data rather than eagerly flushing data to NVMM, we cannot be absolutely sure that all data becomes durable before a failure. 

我们提出的办法是

> Leverage an error checking code in software.
>
> <u>Error checking code</u>：meta-data(checksum) that the program will compute and maintain

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191007224206691.png" alt="image-20191007224206691" style="zoom:50%;" />

如上图所示(a)所示，整个代码被分成3个LP区域，每个区域都有store to memory的操作

对于每一个region的stores操作集合,<u>a checksum calculation</u> is added to the program that summarizes the content of all the written data

考虑第2个region, 这个region有4次store操作(A,B,C,A)，根据每个address的最后一次store操作的值，来计算checksum。如图(b)所示，checksum calculation(X)根据address最后一次store的value计算出来，然后存入persist memory.

我们假设每个programmer(directly or through a library)会在每个region加入这种checksum calculation以及a location in memory to hold all of the checksums



how to use checksum

> If a failure occurs during or after this region, some of the writes (A, B, or C) or the checksum (X) may fail to become durable.
>
> To detect this case, data from relevant memory locations(A, B and C) is read; 
>
> only store values that became durable prior to the failure are read out from the NVMM, other values were lost. 
>
> 然后用他们计算一个checksum并且和saved checksum对比。如果他们不匹配，我们可以确定some of the data or the checksum failed to persist before the failur. Then, we must recover.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191007233250800.png" alt="image-20191007233250800" style="zoom:50%;" />

Recover:

> If a Region is found to be inconsistent with its checksum, a recovery action must be invoked. 
>
> Recovery mechanisms are Region and workload dependent, and the programmer must implement a suitable recovery approach for each Region.



### B. Lazy vs. Eager Persistency

<img src="/Users/jones/Desktop/屏幕快照 2019-10-07 下午11.45.29.png" alt="屏幕快照 2019-10-07 下午11.45.29" style="zoom:50%;" />

### C. Persistency Region Choices

What regions are suitable for lazy persistency?

> * there cannot be data dependences between regions
>
> With Lazy Persistency, they may be persisted out of order, depending on which computation results and checksums are **<u>evicted from the cache first</u>**.  LP恢复写的顺序并不是按照代码顺序来的，所以数据之间互相依赖的话，会产生问题
>

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191007234824937.png" alt="image-20191007234824937" style="zoom:50%;" />

