---
title: CA - Storage
date: 2019-12-08 15:39:48
tags: Computer Architecture
categories: Computer Architecture
index_img: /img/image-20191209103141041.png
---

# 1. Introduction

<center><img src="image-20191208134608723.png" alt="image-20191208134608723" style="zoom:50%;" /></center>

## 1.1 缓存性能

CPU execution time

$$=(CPU\, clock\, cycles + Memory\, stall\, cycles)\times Clock\, cycle\, time$$

{:.warning}

这里CPU clock cicles包括handle cache hit/miss的时间

<center><img src="image-20191208135106480.png" alt="image-20191208135106480" style="zoom:60%;" /></center>
<center><img src="image-20191208135244903.png" alt="image-20191208135244903" style="zoom:60%;" /></center>

看一道例题

> a computer with CPI=1 when cache hit.  
>
> 50% instructions are loads and stores;
>
> 2% miss rate, 25 cc miss penalty;
>
> **Q:** how much faster would the computer be if all instructions were cache hits?

Answer:

1. always hit:

CPU execution time = (CPU clocks cycles + Memory stall cycles) * clock cycle

=$$(IC \times CPI + 0) \times clock\,cycle$$

=$$IC \times clock\, cycle$$

<br/>

2. with misses

Memory stall cycles

= $IC \times \frac{Memory\, accesses}{Instruction}\times Miss\, rate\times Miss\, penalty$

=$IC\times(1+0.5)\times 0.02\times 25$

=$IC\times 0.75$



memory accesses=1.5是因为执行任何一条指令都要访问memory取指令，并且50%的指令是load, store 因此 1+0.5=1.5



CPU execution time = (CPU clocks cycles + Memory stall cycles) $\times$ clock cycle

=$(IC\times 1.0+IC\times 0.75)\times$clock cycle

=$1.75\times $clock cycle

所以比值是1.75



## 1.2 4个存储器层次结构问题

Q1: Where can a block be placed in the upper level? (block placement)

Q2: How is a block found if it is in the upper level? (block identification)

Q3: Which block should be replaced on a miss? (block replacement)

Q4: What happens on a write? (write strategy)

<center><img src="image-2019120874232.png" alt="截屏2019-12-08下午7.42.32" style="zoom:67%;" /></center>
<center><img src="image-20191208194519623.png" alt="image-20191208194519623" style="zoom: 67%;" /></center>

### A. Write Strategy

`Write hit`

* write-through: info is written to both the block in the cache and to the block in the lower-level memory
* write-back: info is written only to the block in the cache;  to the main memory only when the modified cache block is replaced[dirty bit]

`Write miss`

* Write allocate: data at the missed-**write** location is loaded to cache, followed by a **write**-hit operation  
* No-write allocate[write around]: data at the missed-**write** location is not loaded to cache, and is written directly to the backing store.  ;  *until the program tries to read the block, the data is loaded to cache;*



<center><img src="image-20191208201918610.png" alt="image-20191208201918610" style="zoom:50%;" /></center>

1. No-Write allocate:  4 misses + 1 hit

<center><img src="image-20191208202025669.png" alt="image-20191208202025669" style="zoom:50%;" /></center>

2. Write allocate:  2 misses + 3 hits

<center><img src="image-20191208202722945.png" alt="image-20191208202722945" style="zoom:50%;" /></center>

# 2. 缓存性能



### Hit or Miss: How long will it take?

Average memory access time = Hit time + Miss rate x Miss penalty

* **Example**

> 16KB instr cache + 16KB data cache;
>
> or, 32KB unified cache;
>
> 36% data transfer instructions;
>
> (load/store takes 1 extra cc on unified cache)
>
> 1 CC hit; 200 CC miss penalty;

<center><img src="image-20191208204528711.png" alt="image-20191208204528711" style="zoom:30%;" /></center>

> **Q1:** split cache or unified cache has lower miss rate? 

Answer:

<center><img src="image-20191208204804983.png" alt="image-20191208204804983" style="zoom:40%;" /></center>

1. split cache

16KB instruction Miss rate

​		= $$\frac{3.82}{1000}/1=0.004$$

16KB data miss rate

​		=$$\frac{40.9}{1000}/0.36=0.114$$

assume 74% of memory accesses are instruction references

Overall miss rate

​		=$$(74\%\times 0.004)+(26\%\times 0.114)=0.0326$$

2. unified cache

Miss rate

=$$\frac{43.3}{1000}/(1.0+0.36)=0.0318$$

> **Q2:** average memory access time?

<center><img src="image-20191208205644462.png" alt="image-20191208205644462" style="zoom:40%;" /></center>
<center><img src="image-20191208205754992.png" alt="image-20191208205754992" style="zoom:40%;" /></center>

## 2.1 存储器平均访问时间与处理器性能



# 3. Six Basic Cache Optimizations  

我们将所有缺失分成三类

* 强制缺失[Compulsory]

  cold-start/first-reference misses;

* 容量缺失[Capacity]

  cache size limit;

   blocks discarded and later retrieved;

* 冲突缺失

  collision misses: associativity

  a block discarded and later retrieved in a set;

<center><img src="image-20191208213224033.png" alt="image-20191208213224033" style="zoom:30%;" /></center>
<center><img src="image-20191208213207928.png" alt="image-20191208213207928" style="zoom:30%;" /></center>
<center><img src="image-20191208213148539.png" alt="image-20191208213148539" style="zoom:30%;" /></center>

## 3.1 Larger Block size

* **Reduce** compulsory misses

  ​	Leverage spatial locality

* **Reduce** static power

  ​	block size增大，地址里面index位就变多，tag位数就变少，比较时需要的工作量就变少

* **Increase** conflict/capacity misses

  ​	Fewer block in the cache

<center><img src="image-20191208214954621.png" alt="image-20191208214954621" style="zoom: 30%;" /></center>

#### Example

<center><img src="image-20191208215035983.png" alt="image-20191208215035983" style="zoom:40%;" /></center>

**Answer**

 avg mem access time

​		=hit time + miss rate x miss penalty

{% note info %}

 assume 1-CC hit time

 for a 256-byte block in a 256 KB cache:

 avg mem access time

​		= 1 + 0.49% x (80 + 2x256/16) = 1.5 cc

{% endnote %}

 2x256/16是因为存储器2cc能给cache传回16bytes



## 3.2 Larger cache

* **Reduce** capacity misses

* **Increase** hit time, cost, and power



## 3.3 Higher Associativity

* **Reduce** conflict misses

* **Increase** hit time



## 3.4 Multilevel cache

* **Reduce** miss penalty

<br/>

#### A. Two-level cache

 Add another level of cache between the original cache and memory

* **L1**: small enough to match the clock cycle time of the fast processor;

* **L2**: large enough to capture many accesses that would go to main memory, lessening miss penalty

<center><img src="image-20191208220714030.png" alt="image-20191208220714030" style="zoom:50%;" /></center>

#### B. Average memory access time

=Hit timeL1 + Miss rateL1 x Miss penaltyL1

=Hit timeL1 + Miss rateL1

 x(Hit timeL2+Miss rateL2xMiss penaltyL2)



#### C. Average mem stalls per instruction

=Misses per instructionL1 x Hit timeL2

 \+ Misses per instrL2 x Miss penaltyL2



#### D. Local miss rate

 the number of misses in a cache

 divided by the total number of mem accesses to <u>this cache</u>;

 {:.info}

分成 Miss rateL1, Miss rateL2

#### E. Global miss rate

 the number of misses in the cache 

 divided by the number of mem accesses generated by the processor;

 {:.info}

L1的全局缺失率Miss rate**L1**,<u>L2的全局缺失率 Miss rateL1 x Miss rateL2</u>



#### Example

> 1000 mem references -> 40 misses in L1 and 20 misses in L2;
>
> miss penalty from L2 is 200 cc;
>
> hit time of L2 is 10 cc;
>
> hit time of L1 is 1 cc;
>
> 1.5 mem references per instruction;

>  **Q: 1.** various miss rates?

 **L1:** local = global

 40/1000 = 4%

 **L2:**

 local: 20/40 = 50%

 global: 20/1000 = 2%

> **Q: 2.** avg mem access time?

average memory access time

=Hit timeL1 + Miss rateL1

 x(Hit timeL2+Miss rateL2xMiss penaltyL2)

=1 + 4% x (10 + 50% x 200)

=5.4

>  **Q: 3.** avg stall cycles per instruction?

average stall cycles per instruction

=Misses per instructionL1 x Hit timeL2

 \+ Misses per instrL2 x Miss penaltyL2

=(1.5x40/1000)x10+(1.5x20/1000)x200

=6.6



## 2.5  Prioritize read misses over writes

* **Reduce** miss penalty

{% note info %}

这种方法使得在write buffer将数据写入memory之前，就可以为read操作提供服务

{% endnote %}

## 2.6 Avoid address translation during indexing cache

虚拟缓存





# 4. Ten advanced cache optimizations

<center><img src="image-20191209100308445.png" alt="image-20191209100308445" style="zoom: 33%;" /></center>

## 4.1 Small and Simple First-Level Caches

* Small size

> support a fast clock cycle
>
> reduce power

* Lower associativity

> reduce both hit time and power
>
> (direct-mapped caches can overlap the tag check with the transmission of the data)



## 4.2 Way prediction

•Reduce conflict misses and hit time

•**Way prediction**

 *block predictor bits* are added to each block to predict the way/block within the set of the *next* cache access

 

 the multiplexor is set **early to select the desired block**;

 only a single tag comparison is performed **in parallel with cache reading**;

 a miss results in checking the other blocks for matches in the next clock cycle;

## 4.3 Pipelined Cache Access

* Increase cache bandwidth

* Higher latency

* Greater penalty on mispredicted branches and more clock cycles between issuing the load and using the data 

## 4.4 Nonblocking caches

> 对于允许乱序执行的流水化计算机，他的处理器不必因为一次数据缓存缺失而停顿。在等待数据缓存返回缺失数据时，处理器可以继续从指令缓存中提取指令。nonblocking cache允许数据缓存在一次缺失期间继续提供缓存命令

* Increase cache bandwidth



## 4.5 Multibanked caches

> Divide cache into independent banks that support simultaneous accesses
>
> Sequential interleaving spread the addresses of blocks sequentially across the banks

<center><img src="image-20191217202233856.png" alt="image-20191217202233856" style="zoom:50%;" /></center>

* Increase cache bandwidth

## 4.6 Critical Word First & Early Restart

通常CPU只会request一个word, 但是一个cache line对应了很多个word

* critical word first

> 首先请求critical word也就是CPU request的那个word, 然后发送给CPU, 然后再去请求一个cache line剩余的部分

* early restart

> 按正常顺序获取word, 不需要等待一个cache line全部放入缓存再发送给处理器，而是critical word到了就直接发送给处理器，不需要等待还没有完成传输的word





* Reduce miss penalty

## 4.7 Merging Write buffer

Write merging merges four entries (with sequential addresses)  into a single buffer entry

<center><img src="image-20191217203849064.png" alt="image-20191217203849064" style="zoom:50%;" /></center>

* Reduce miss penalty

## 4.8 Compiler optimizations

### A. Loop interchange

#### before

```cpp
for(j = 0; j < 100; j++) {
  for(i = 0; i < 5000; i++) {
    x[i][j] = 2 * x[i][j];
  }
}
```

x[i]\[j]访问之后访问x[i+1]\[j],这中间差了100个数据

#### after

```cpp
for(i = 0; i < 5000; i++) {
  for(j = 0; j < 100; j++) {
    x[i][j] = 2 * x[i][j]
  }
}
```

x[i]\[j]访问之后访问x[i]\[j+1], 这中间只差了一个数据，就可以按照数据存储的顺序来访问，增强了space locality。

这样一次miss之后，load多个word进cache的话，缺失就会变少



### B. Blocking[分块]

#### before

```cpp
for(i = 0; i < N; i++) {
  for(j = 0; j < N; j++) {
    {
      r = 0;
      //y的行 * z的列
      for(k = 0; k < N; k++) {
        r = r + y[i][k] * z[k][j]
      }
    }
  }
}
```

<img src="image-20191217210134473.png" alt="image-20191217210134473" style="zoom:50%;" />

#### after

*maximize accesses to loaded data before they are replaced*

```cpp
for(jj = 0; jj < N; jj = jj + B) {
  for(kk = 0; kk < N; kk = kk + B) {
    for(i = 0; i < N; i++) {
      for(j = jj; j < min(jj+B, N); j++) {
        {
          r = 0;
          for(k = kk; k < min(kk); k++) {
            r = r + y[i][k] * z[k][j];
          }
          x[i][j] = x[i][j] + r;
        }
      }
    }
  }
}
```

<img src="image-20191217210322625.png" alt="image-20191217210322625" style="zoom:50%;" />

## 4.9 Hardware prefetching

指令和数据都可以预取，既可以直接放在cache中，也可以放在一个访问速度快于main memory的外部缓冲区中。

下面来看一下指令预取

通常，处理器在一次缺失时提取两个块。被请求块和下一个相邻块

* 被请求块放在他返回时的指令缓存中
* 预取块放在指令流缓冲区中。

请求时，如果发现被请求块位于指令流缓冲区，那么原缓存请求取消，从流缓冲区来读取这个块。并发出下一条预取请求

* Reduce miss penalty/rate



## 4.10 Compiler Prefetching

作为hardware prefetching的替代方法。可以在处理器需要某一数据之前，由编译器插入请求该数据的预取指令

有以下两种prefetch

* **Register** **prefetch**

  load the value into a register

* **Cache** **prefetch**

  load data into the cache





**Example**

16-byte blocks;

8-byte elements for a and b;

write-back strategy

a\[0][0] miss, copy both a\[0][0],a\[0][1] as one block contains 16/8 = 2;

***before***

```cpp
for(i = 0; i < 3; i = i + 1)
  for(j = 0; j < 100; j = j + 1)
    a[i][j] = b[j][0] * b[j + 1][0]
```

缺失次数

对于a, $3\times(100/2)=150$次缺失

对于b, b不会从空间局部性受益，但是可以从时间局部性受益

b从b[0]\[0]访问到b[100]\[0]一共有101次缺失

总共251次缺失

***after prefetching***

```cpp
for(j = 0; j < 100; j++) {
  prefetch(b[j+7][0]);
  prefetch(a[0][j+7]);
  a[0][j] = b[j][0] * b[j+1][0];
}
for(i = 1; i < 3; i++) {
  for(j = 0; j < 100; j++) {
		prefetch(a[i][j+7]);
    a[i][j] = b[j][0] * b[j+1][0];
  }
}
```

修改后，将会预取a[i]\[7]---a[i]\[99]和b[7]\[0]--b[100]\[0]

所以非预取缺失只会出现在前几个循环

* 第一个loop访问b[0]\[0]---b[6]\[0]的7次缺失
* 第一个loop访问a[0]\[0]--a[0]\[7]的4次缺失
* 第二个loop访问a[1]\[0]--a[1]\[6]的4次缺失
* 第二个loop访问a[2]\[0]--a[2]\[6]的4次缺失

总共有19次非预取缺失



**Reduce** miss penalty/rate

# 5. Memory

Performance Measures

* **Latency**

{% note warning%}

 the time to retrieve the first word of the block

{% endnote %}

 important for caches;

 harder to reduce;

> **access time**: the time between when a read is requested and when the desired word arrives;
>
> **cycle time**: the minimum time between unrelated requests to memory;
>
> *or* the minimum time between the start of an access and the start of the next access;

* **Bandwidth** 

the time to retrieve the rest of this block



## 5.1 RAM

<a href="https://www.zhihu.com/question/30492703">Zhihu Birkee's answer</a>

RAM，Random-Access Memory，即随机存取存储器，其实就是内存，断电会丢失数据。
主要分为SRAM（static）和DRAM（dynamic)。主要的区别在于存储单元，DRAM使用电容电荷进行存储。需要一直刷新充电。SRAM是用锁存器锁住信息，不需要刷新。但也需要充电保持。

<center><table>
  <tr>
    <td><div class="card">   
  <div class="card__image">     
  <img class="image" src="image-20191209104433202.png" alt="image-20191209104433202" style="zoom:50%;" /> 
  </div>   
  <div class="card__content">     
    <div class="card__header">       
      <h4>DRAM的基本存储单元</h4>     
    </div>     
    <p>利用一个晶体管进行控制电容的充放电</p>  
  </div>
</div></td>
  <td><div class="card">   
  <div class="card__image">     
  <img class="image" src="image-20191209104458720.png" alt="image-20191209104458720" style="zoom:50%;" /> 
  </div>   
  <div class="card__content">     
    <div class="card__header">       
      <h4>DRAM一般的寻址模式</h4>     
    </div>     
    <p>控制的晶体管集成在单个存储单元中</p>  
  </div>
 </div></td>
  </tr>
</table></center>




## 5.2 SRAM for cache[Static Random Access Memory]

* Six transistors per bit to prevent the information from being disturbed when read

* Don’t need to refresh, so access time is very close to cycle time



## 5.3 DRAM for main memory[Dynamic Random Access Memory]

<center><img src="image-20191209105411600.png" alt="image-20191209105411600" style="zoom:30%;" /></center>
<center><img src="image-20191209105418930.png" alt="image-20191209105418930" style="zoom:30%;" /></center>
<p>bing row into row buffer</p>
<center><img src="image-20191209105429474.png" alt="image-20191209105429474" style="zoom:30%;"/></center>
<p>select Data via Multiplexor</p>
<center><img src="image-20191209105442259.png" alt="image-20191209105442259" style="zoom:30%;"/></center>
<p>Data selected</p>
<center><img src="image-20191209105451917.png" alt="image-20191209105451917" style="zoom:30%;"/></center>
<p>Row buffer hit</p>
<center><img src="image-20191209105459461.png" alt="image-20191209105459461" style="zoom:30%;"/></center>
<p>Row buffer conflict</p>



<br>




## 5.6 提高存储器的可靠性

### Error type

* **Soft errors**

{% note info %}

 changes to a cell’s contents, not a change in the circuitry

{% endnote %}

* **Hard errors**

{% note info%}

 permanent changes in the operation of one or more memory cells

{% endnote %}

### Error detection and fix

<center><table>
  <tr>
    <td><div class="card">   
  <div class="card__content">     
    <div class="card__header">       
      <h4>Parity only</h4>     
    </div>     
    <p>only one bit of overhead to detect a single error in a sequence of bits;</p>  
  </div>
</div></td>
  <td><div class="card">    
  <div class="card__content">     
    <div class="card__header">       
      <h4>ECC only</h4>     
    </div>     
    <p>detect two errors and correct a single error with 8-bit overhead per 64 data bits</p>  
  </div>
 </div></td>
    <td><div class="card">    
  <div class="card__content">     
    <div class="card__header">       
      <h4>Chipkill</h4>     
    </div>     
    <p>类似于在磁盘中使用RAID方法，它分散数据和ECC信息，在单个存储器芯片完全失效时，可以从其余存储器芯片中重构丢失数据</p>  
  </div>
 </div></td>
  </tr>
</table></center>




# 6. Disk





<div class="item">   
  <div class="item__image"> 
    <img class="image image--lg" src="image-20191209113803942.png" alt="image-20191209113803942" style="zoom:50%;" />
    <img class="image image--lg" src="image-20191209113823956.png" alt="image-20191209113823956" style="zoom:50%;" />   
  </div>   
  <div class="item__content">     
  <div class="item__header">       
    <h4>Disk</h4>     
  </div>     
  <div class="item__description">     
    <p>Sector: minimum storage unit. A block may span multiple sectors</p>
     <p>Cluster:(dis)contiguous groups of sectors to reduce the overhead of managing on-disk data structures; may span more than one track</p>    
  </div>   
  </div> 
</div>


Areal density = $\frac{Tracks}{Inch}$ on a disk surface $\times$ $\frac{Bits}{Inch}$ on a track



## 6.1 RAID[Redundant Arrays of Inepensive Disk]

* RAID 0: No Redundancy
* RAID 1: Mirroring / Shadowing

> Two copies for every piece of data
>
> one logical write = two physical writes
>
> 100% capacity/space  overhead

<center><img src="IMG_2C62CE9D9D16-1.jpeg" alt="IMG_2C62CE9D9D16-1" style="zoom:50%;" /></center>

* RAID 2:

> Each bit of data word is written to a data disk drive
>
> Each data word has its (Hamming Code) ECC word recorded on the ECC disks
>
> On read, the ECC code verifies correct data or corrects single disks errors

<center><img src="image-20191216120558419.png" alt="image-20191216120558419" style="zoom:50%;" /></center>

* RAID 3:

> RAID 3 P校验盘存的是前面所有盘数据的和。
>
> 他很慢，因为每读取一次磁盘数据，校验时要读取其他所有磁盘才能算校验和
>
> 磁盘坏了的话，也可以通过checksum恢复数据

<center><img src="IMG_E9E135911744-1.png" alt="IMG_E9E135911744-1" style="zoom:50%;" /></center>
<center><img src="image-20191216121018499.png" alt="image-20191216121018499" style="zoom:50%;" /></center>

* RAID 4

<center><img src="IMG_17517EAA2585-1.png" alt="IMG_17517EAA2585-1" style="zoom: 33%;" /></center>

* RAID 5

<center><img src="IMG_8BFCBD97FD85-1.png" alt="IMG_8BFCBD97FD85-1" style="zoom:30%;" /></center>

* RAID 6

> row parity

<center><img src="image-20191216130127847.png" alt="image-20191216130127847" style="zoom:30%;" /></center>

> Diagonal parity

<center><img src="image-20191216130657742.png" alt="image-20191216130657742" style="zoom:30%;" /></center>

看一个例子

disk 1和disk 3 double failure

<center><img src="image-20191216130845614.png" alt="image-20191216130845614" style="zoom:30%;" /></center>

First recover Disk 3 stripe 0. Because its diagonal parity is independent from the other failed disk 1.

<center><img src="image-20191216130921114.png" alt="image-20191216130921114" style="zoom:30%;" /></center>

When Disk 3 stripe 0 is recoverd, then we can recover Disk 1 stripe 3. Because on its row, only its stripe is failed. So we can use the row parity to recover the stripe.

<center><img src="image-20191216130953500.png" alt="image-20191216130953500" style="zoom:30%;" /></center>

Now, we have recovered Disk 1 stripe 3 and Disk 3 stripe 0

<center><img src="image-20191216131127165.png" alt="image-20191216131127165" style="zoom:30%;" /></center>

以下同理

<center><img src="image-20191216131242663.png" alt="image-20191216131242663" style="zoom:30%;" /></center>
<center><img src="image-20191216131302430.png" alt="image-20191216131302430" style="zoom:30%;" /></center>
<center><img src="image-20191216131334869.png" alt="image-20191216131334869" style="zoom:30%;" /></center>
<center><img src="image-20191216131357343.png" alt="image-20191216131357343" style="zoom:30%;" /></center>
<center><img src="image-20191216131420435.png" alt="image-20191216131420435" style="zoom:30%;" /></center>
<center><img src="image-20191216131449208.png" alt="image-20191216131449208" style="zoom:30%;" /></center>
<center><img src="image-20191216131514362.png" alt="image-20191216131514362" style="zoom:30%;" /></center>
<center><img src="image-20191216131536642.png" alt="image-20191216131536642" style="zoom:30%;" /></center>
<center><img src="image-20191216131556362.png" alt="image-20191216131556362" style="zoom:30%;" /></center>

# 7. I/O performance

### unique measures

* Diversity

> which I/O devices can connect to the computer system?

* Capacity

> how many I/O devices can connect to a computer system?

### Metrics

* response time[latency]

* throughout[bandwidth]

<center><img src="image-20191216102829183.png" alt="image-20191216102829183" style="zoom:50%;" /></center>

## 7.1 Throughout VS Response Time

***Transaction***

> An interation between human and computer is called ***<u>transaction</u>***

A ***<u>transaction time</u>*** is divided into three parts:

> * <u>entry time</u>
>
> > The time for user to enter the command
>
> * <u>system response time</u>
>
> > The time between when the user enters the command and the complete response is displayed
>
> * <u>think time</u>
>
> > The time from the reception of the response until the user begins to ***<u>enter the next command</u>***

<center><img src="image-20191216210721189.png" alt="image-20191216210721189" style="zoom:40%;" /></center>
<center><img src="image-20191216210659441.png" alt="image-20191216210659441" style="zoom:50%;" /></center>

More transaction time reduction than just the response time reduction. People need less time to think when given a faster response



## 7.2 Transaction-Processing Benchmarks

### A. SPEC

### B. TPC_C



## 7.3 A little Queuing Theory[to calculate response time and throughput]

<center><img src="image-20191216104938324.png" alt="image-20191216104938324" style="zoom:50%;" /></center>

***Flow-balanced State***

* If the system is in **steady state**,  then the number of tasks entering the system must equal the number of tasks leaving the system

* This **flow-balanced state** is necessary but not sufficient for steady state

* The system has reached **steady state** if the system has been observed for a sufficiently long time and  mean waiting times stabilize



### A. little's law[important]

#### i. Assumptions

{%note info%}

 input rate = output rate; 

 a steady supply of tasks independent for how long they wait for service;

{% endnote %}

#### ii. little's law

{%note error%}

Mean number of tasks in system =  Arrival rate $\times$ Mean response time

注意arrival rate 表示单位时间到了几个task

{%endnote%}

#### iii. single-server model

<center><img src="image-20191216214128113.png" alt="image-20191216214128113" style="zoom: 40%;" /></center>

* $Time\_{server}$  —Average time to service a task; average  $service\_{rate}=1/Time\_{server}$
* $Time_{queue}$—Average time per task in the queue.
* $Time\_{system}$ —Average time per task in the system, or the response time, which is $Time\_{queue}+Time\_{server}$ .
* Arrival rate—Average number of arriving tasks/second
* $Length_{server}$—Average number of tasks in service.
* $Length_{queue}$—Average length of queue.
* $Length\_{system}$—Average number of tasks in system, which is $Length\_{server}+Length\_{queue}$

***Server Utilization***

{% note info %}

Server utilization = Arrival rate$\times$ $Time_{server}$

{% endnote %}

<center><img src="image-20191216110406290.png" alt="image-20191216110406290" style="zoom:50%;" /></center>

<br/>

***Time queue***

$Time\_{queue}=Length\_{queue}\times Time\_{server}+$Mean time to complete the task being serviced when new task arrives if server is busy

$Time\_{queue}=Time\_{server}\times \frac{Server\;utilization}{1-Server\; utilization}$

$$\begin{equation}\begin{aligned}Length_{queue}=&Arrival\; rate\times Time_{server}\times\frac{Server\;utilization}{1-Server\; utilization}\\ =&\frac{Server\; utilization^2}{1-Server\; utilization}\end{aligned}\end{equation}$$



#### iv. M/M/1 queue

> **M**: *Markov*
>
> exponentially random request arrival;
>
> **M**: *Markov*
>
> exponentially random service time
>
> **1**
>
> single server

**assumptions**

> The system is in equilibrium
>
> *Interarrival* *times* [times between two successive requests arriving] are exponentionally distributed
>
> *Infinite population model*: unlimited number of sources of requests
>
> Server starts on the next job immediately after finishing prior one
>
> FIFO queue with unlimited length
>
> One server only

<center><img src="image-20191216222544839.png" alt="image-20191216222544839" style="zoom:50%;" /></center>
<center><img src="image-20191216222600418.png" alt="image-20191216222600418" style="zoom:50%;" /></center>
<center><img src="image-20191216222618484.png" alt="image-20191216222618484" style="zoom:50%;" /></center>

#### v. M/M/m queue

<center><img src="image-20191216222740597.png" alt="image-20191216222740597" style="zoom:50%;" /></center>
<center><img src="image-20191216222905001.png" alt="image-20191216222905001" style="zoom:40%;" /></center>