---
title: Memory Management
date: 2019-11-22 14:33:11
index_img: /img/image-20191125120512021.png
tags: [OS, Memory]
categories: OS
---

<!--more-->

<style>
  .page__header .header__brand path {
    fill: rgba(255, 255, 255, .95);
  }
</style>


<br/>

# 1. 虚拟内存基础

## 1.1 物理和虚拟寻址

主存[main memory]是一个由M个连续的byte组成的数组。***每个byte都有一个物理地址与他对应***。比如第1个byte对应0的物理地址，第二个byte对应1的物理地址。

可以使用物理地址访问内存。如图，CPU向主存请求物理地址4为起点的4个bytes。

CPU执行这条加载指令时

* 会生成一个有效物理地址，通过内存总线，传递给主存
* 主存取出数据返回给CPU
* CPU将数据放在一个寄存器里。

<center><img src="image-20191122221045486.png" alt="image-20191122221045486" style="zoom: 67%;" /></center>

现在计算机都会使用虚拟寻址

CPU生成一个虚拟地址来访问主存

这个虚拟地址在被送到内存之前先转换成适当的物理地址，这个过程叫做地址翻译。需要CPU芯片上的MMU于OS合作

<center><img src="image-20191122222054227.png" alt="image-20191122222054227" style="zoom:50%;" /></center>

<br/>

## 1.2 地址空间

地址空间是以一个非负整数地址的有序集合：$\{0,1,2,\cdots\}$，

如果地址空间中的整数是连续的，那么我们说它是一个线性地址空间。

<br/>

在一个带虚拟内存的系统中，CPU从一个有$N=2^n$个地址的地址空间中生成虚拟地址，虚拟地址空间的集合为$\{0,1,2,\cdots,N-1\}$，这个虚拟地址空间就叫做一个n位地址空间，现代系统通常支持32位或者64位虚拟地址空间

物理地址空间对应于物理内存的M个字节$\{0,1,2,\cdots,M-1\}$, M并不要求是2的幂，为了简化问题，我们假设$M=2^m$

<center><img src="image-20191123110239273.png" alt="image-20191123110239273" style="zoom:50%;" /></center>

<br/>

## 1.3 虚拟内存作为缓存的工具

磁盘上的数据被分割成块，块是磁盘是主存之间的传输单元。

内存又是怎么分割的呢？

### &emsp;&emsp;分页

页面是主存物理空间中划分出来的等长的固定区域。分页方式的优点是页长固定，因而便于构造页表、易于管理，且不存在外碎片。但分页方式的缺点是页长与程序的逻辑大小不相关。例如，某个时刻一个子程序可能有一部分在主存中，另一部分则在辅存中。这不利于编程时的独立性，并给换入换出处理、存储保护和存储共享等操作造成麻烦。

### &emsp;&emsp;分段

段是按照程序的自然分界划分的长度可以动态改变的区域。通常，程序员把子程序、操作数和常数等不同类型的数据划分到不同的段中，并且每个程序可以有多个相同类型的段。

 段表本身也是一个段，可以存在辅存中，但一般是驻留在主存中。将用户程序地址空间分成若干个大小不等的段，每段可以定义一组相对完整的逻辑信息。存储分配时，以段为单位，段与段在内存中可以不相邻接，也实现了离散分配。

(1)    段的逻辑独立性使其易于编译、管理、修改和保护，也便于多道程序共享。

(2)    段长可以根据需要动态改变，允许自由调度，以便有效利用主存空间。

(3)    方便编程，分段共享，分段保护，动态链接，动态增长

 因为段的长度不固定，段式虚拟存储器也有一些缺点：

(1)    主存空间分配比较麻烦。

(2)    容易在段间留下许多碎片，造成存储空间利用率降低。

(3)    由于段长不一定是2的整数次幂，因而不能简单地像分页方式那样用虚拟地址和实存地址的最低若干二进制位作为段内地址，并与段号进行直接拼接，必须用加法操作通过段起址与段内地址的求和运算得到物理地址。因此，段式存储管理比页式存储管理方式需要更多的硬件支持。

<table>
  <tr>
    <td><img src="image-20191125130737955.png" alt="image-20191125130737955" style="zoom:50%;" /></td>
    <td><img src="image-20191125130301483.png" alt="image-20191125130301483" style="zoom: 33%;" /></td>
  </tr>
</table>






VM将虚拟内存分割成虚拟页[Virtual Page, VP]，每一个page是磁盘中一个块的大小，虚拟页的大小为$P=2^p$。同样物理内存也被分割为物理页[Physical Page, PP 也被称为页帧page frame]，大小也为P bytes



* ### intel pentium[支持分页加分段]

<center><img src="image-20191202130520024.png" alt="image-20191202130520024" style="zoom:50%;" /></center>


#### A. pentium分段：

pentium允许一个段段大小最多可达4GB，每个进程最多的段段数量为16K个

> 进程的逻辑地址空间分成两部分
>
> * 私有：8K个段-----------------信息保存在local descriptor table, LDT中
> * 所有进程共享：8K个段----信息保存在glocal descriptor table, GDT中
>
> LDT，GBT的每个条目为<u>8B</u>，包括一个段段详细信息，如基位置和段界限

***i. <u>逻辑地址</u>是一对[selector, offset]，48bits***

* selector 16bits

<center><img src="image-20191202130545949.png" alt="image-20191202130545949" style="zoom:50%;" /></center>

> 其中s表示段号，g表示段是在GDT还是在LDT中，p表示保护信息

* offset 32bits

>  用来表示字节[或者 字]在段内的位置

机器有6个段寄存器，允许一个进程同时访问6个段。

它还有6个8B的微程序寄存器，用来保存相应的来自LDT或GDT的描述符。这个缓冲区允许pentium不必在每次内存引用时都从内存中读取描述符

***ii.pentium<u>线性地址</u> 32bits***

线性地址的形成方式：

> 段寄存器指向LDT或GDT中的适当条目
>
> 段的基地址和界限信息用来产生线性地址
>
> * 界限用来检查地址的合法性。如果地址无效，就产生内存出错；如果有效，偏移值就和基地址的值相加，产生32位线性地址

<center><img src="image-20191202132646744.png" alt="image-20191202132646744" style="zoom:50%;" /></center>


#### B. pentium分页

pentium体系结构允许页的大小为4KB或者4MB。

对于4KB的页，pentium采用二级分页方案，其中32位线性地址按如下形式划分

<center><img src="image-20191202133553560.png" alt="image-20191202133553560" style="zoom:50%;" /></center>

最高10位引用最外层页表的条目，被称为page directory。

page directory线性地址中内存的10位内容索引的内部页表。最低的11位是页表项所指向的4KB页面内的偏移。

页目录中的一个条目是page size标志，如果设置了这个标志，就表示页帧的大小为4MB，page directory直接指向4MB的页帧，绕过了内层页表

<center><img src="image-20191202133744592.png" alt="image-20191202133744592" style="zoom:50%;" /></center>

### back to 分页

任意时刻，虚拟页中的页都属于以下三种状态中的一种

* 未分配的：没有磁盘数据与其对应
* 缓存的：对应磁盘中的一个块，并且缓存进了物理内存
* 未缓存的：对应磁盘中的一个块，但是还没有缓存进物理内存

<center><img src="image-20191122231457120.png" alt="image-20191122231457120" style="zoom:50%;" /></center>

### 1.3.1 page table

如下图所示，page table是page table entry组成的数组。他是被存在内存中的

一个page table entry[PTE]有一个有效位和一个物理页号。

如果有效位是1，说明他已缓存

如果有效位是0

* null代表未分配的
* 如果是未缓存的，这个地址指向该虚拟页在**磁盘**上的起始位置



<center><img src="image-20191122231829304.png" alt="image-20191122231829304" style="zoom: 50%;" /></center>

#### A. page hit

如果CPU想要读在VP2中的虚拟内存中的一个word

* MMU将虚拟地址作为索引来定位PTE2，并从内存中读取PTE2

* 因为PTE2的有效位是1，所以MMU知道了VP2已经缓存在内存中了。它使用PTE2中的物理页号，构造出这个word的物理地址

<center><img src="image-20191122232635336.png" alt="image-20191122232635336" style="zoom:50%;" /></center>

#### B. page fault

CPU想要访问VP3中的一个word,但是VP3并未缓存在内存中

* MMU从内存中读取PTE3，从有效位推断VP3未被缓存，触发缺页异常
* 缺页异常调用内核中的缺页异常处理程序，这个程序选择一个牺牲页，比如放在PP3中的VP4。并且修改page table entry，反映出VP4不再缓存在主存里了
  * 如果VP4已经被修改了，内核就会将其复制回磁盘

<center><img src="image-20191122233502102.png" alt="image-20191122233502102" style="zoom: 50%;" /></center>

* 内核从磁盘复制VP3到内存中的PP3，更新PTE3，然后返回
* 异常处理程序返回时，它会重新启动导致缺页的指令
* 重复同上page hit的过程



#### C. 分配页面

<center><img src="image-20191122235308295.png" alt="image-20191122235308295" style="zoom:50%;" /></center>

#### 虚拟内存效率怎么样呢？

因为***局部性原理***，虚拟内存工作得非常好

尽管在程序运行过程中程序饮用的不同页面的总数可鞥超出物理内存总的大小，但是局部性原理保证在任意时刻，程序去相遇在一个较小的活动页面[active page]集合上工作，这个集合叫做***工作集***[working set]或者***常驻集合***[resident set]

在初始开销，也就是将工作集页面调度到内存中之后，接下来对这个工作集的引用就会hit,不会产生额外的磁盘流量

但是如果工作集的大小超过了物理内存的大小，就会产生***抖动***[thrashing],这个时候页面会不断换进换出，程序运行的非常慢



## 1.4 虚拟内存作为内存管理的工具

操作系统位每个进程提供了一个独立的页表，也就是一个独立的虚拟地址空间

<center><img src="image-20191123001058693.png" alt="image-20191123001058693" style="zoom: 50%;" /></center>

虚拟内存的作用

> * 简化链接。
>
>   独立地址空间允许每个进程的内存映像都使用相同的格式。如1.2中地址空间的图所示，代码段总是从0x400000开始，数据段跟在代码段之后,...。
>
>   这样的一致性简化了连接器的设计，允许连接器生成完全链接的可执行文件，这些可执行文件独立于独立内存中的代码和数据位置
>
> 
>
> * 简化加载
>
>   虚拟内存使向内存中加载可执行文件和共享对象文件变得容易。
>
>   要把.text和.data加载到一个新创建的进程中，Linux加载器为代码和数据段分配虚拟页，标记为无效的，并将页表中的地址指向目标文件中适当的位置，这样他处于未缓存的状态。
>
>   但是加载器并不做从磁盘到内存复制数据的工作。在每个页被初次使用时，虚拟内存系统会***按需调页***。
>
>   将一组连续的虚拟页映射到任意一个文件中的任意位置的表示法称为***内存映射***，Linux提供`mmap`系统调用，允许应用程序自己做内存映射
>
> * 简化共享
>
>   如果要共享代码和数据，操作系统hi将不同进程中适当的虚拟页映射到相同的物理页面
>
> * 简化内存分配
>
>   虚拟内存为用户提供了一个简单的分配额外内存的机制。比如调用malloc,操作系统分配一个适当数字个连续的虚拟内存页面，并且将它们映射到物理内存中的任意位置的k个任意的物理页面。操作系统并不需要分配k个连续的物理内存页面，页面可以随机的分散在物理内存中。

<br/>

## 1.5 虚拟内存作为内存保护的工具

每次CPU生成一个地址时，地址翻译硬件都会读一个PTE，通过在PTE上添加一些额外的许可位来控制对一个虚拟页面内容的访问



<center><img src="image-20191123112329424.png" alt="image-20191123112329424" style="zoom:50%;" /></center>

如上图示例，每个PTE中加入三个许可位。

* `SUP` 进程是否必须运行在内核模式下才能访问该页
* `READ` 控制读权限
* `WRITE` 控制写权限

如果进程i运行在用户模式下，那么它有读VP0和读写VP1的权限，但是不允许它访问VP2

如果一条指令违反了这些许可条件，CPU就会触发保护故障，将控制传递给一个内核中的一场处理程序。Linux shell一般将这种一场报告为`segmentation fault`



## 1.6 地址翻译

<center><img src="image-20191123114305099.png" alt="image-20191123114305099" style="zoom:50%;" /></center>

地址翻译是一个N元素的VAS中的元素和一个M元素的PAS中元素的映射

$$MAP: VAS\rightarrow PAS\cup \varnothing$$

$$MAP(A)=\{\begin{aligned}&A' if\,A\,in\,physical\,address\,A'\\ &\varnothing if A \,not\,in \,physical\,memory\end{aligned}$$

<center><img src="image-20191123114337785.png" alt="image-20191123114337785" style="zoom:50%;" /></center>

#### A. page hit的步骤

* CPU抛出一个虚拟地址，传给MMU
* MMU生成PTE地址，并从高速缓存/主存请求page table entry
* 告诉缓存/主存向MMU返回该PTE
* MMU根据PTE构造出物理地址，并传给高速缓存/主存
* 高速缓存/主存返回所请求的数据给CPU

<center><img src="image-20191123115551431.png" alt="image-20191123115551431" style="zoom:50%;" /></center>

#### B. Page fault的步骤

* CPU抛出一个虚拟地址，传给MMU
* MMU生成PTE地址，并从高速缓存/主存请求page table entry
* 告诉缓存/主存向MMU返回该PTE
* PTE有效位是0，MMU触发一次异常，传递CPU中的控制到操作系统内核中的缺页异常处理程序
* 缺页处理程序确定物理内存中的牺牲页，如果这个页面已经被修改了，则把它换出到磁盘
* 缺页处理程序页面调入心的页面，并更新内存中的PTE
* 缺页处理程序返回到原来的进程，再次执行导致缺页的指令。

<center><img src="image-20191123120431404.png" alt="image-20191123120431404" style="zoom:50%;" /></center>

### 1.6.1 结合cache和虚拟内存

使用虚拟地址还是使用物理地址来访问SRAM呢？

> 一般使用物理地址
>
> 使用物理寻址，多个进程同时在高速缓存中有存储块和共享来自相同虚拟页面的块成为很简单的事，而且高速缓存无需处理保护问题，因为访问权限的检查是地址翻译的一部分。

<center><img src="image-20191123123805540.png" alt="image-20191123123805540" style="zoom:50%;" /></center>

### 1.6.2 利用TLB加速地址翻译

因为page table放在内存中。每次CPU产生一个虚拟地址，MMU就必须查阅一个PTE。

> 在最糟糕的情况下，这会要求从内存多取一次数据，代价是几十到几百个周期
>
> 如果PTE碰巧缓存在L1中，那么开销就下降到1-2个周期

为了减小开销，很多系统都在MMU中包括了一个关于PTE的小缓存，TLB

如下图，虚拟地址被分成

<center><img src="image-20191123130033197.png" alt="image-20191123130033197" style="zoom:50%;" /></center>

TLB index来索引TLB中的block, TLB tag来检查是否是对应的地址

步骤

* 第一步：CPU产生一个虚拟地址
* 第二、三步：MMU从TLB中取出相应的PTE
* 第四步：MMU将虚拟地址翻译成一个物理地址，并且发送给告诉缓存/主存
* 第五步：告诉缓存/主存将请求的数据返回给CPU

若TLB miss, MMU 必须从L1缓存/主存中取出相应的PTE，新取出的PTE放在TLB中，可能会覆盖一个已经存在的条目

<center><img src="image-20191125112258140.png" alt="image-20191125112258140" style="zoom:50%;"  /></center>

<br/>

### 1.6.3 多级页表

一级页表有什么问题：

> 假设有一个32位的地址空间，4KB的page，4bytes的page table entry
>
> 即使应用需要的只是虚拟地址空间中很小的一部分，也总是需要一个4MB的页表驻留在内存中。

怎么做可以压缩页表呢？----> ***多级页表***

<br/>

> 一级页表中的每个PTE对应虚拟内存中的一个4MB的chunk, 每一个chunk都是由1024个连续的页面组成的。
>
> 如果chunk i中的每个page都没有被分配，那么一级页表PTE i就是空的。
>
> 如果chunk i中至少有一个page被分配了，那么一级PTE i就指向一个二级页表的基址。二级页表中的每个PTE负责映射一个4KB的虚拟内存页面
>
> 一个一级页表和一个二级页表的大小是一样的，都是4KB

这样做能够减少内存要求是因为

* 如果一级页表中的一个PTE是空的，那么相应的二级页表根本就不会存在
* 只有一级页表才需要总是在内存中，虚拟内存系统可以在需要时创建、页面调入、调出二级页表，这就减少了主存的压力，只有最经常使用的二级页表菜需要缓存在主存中



<center><img src="image-20191125112718240.png" alt="image-20191125112718240" style="zoom:50%;" /></center>

<br/>

<center><img src="image-20191125112730298.png" alt="image-20191125112730298" style="zoom:50%;" /></center>




## 1.7 案例：Intel Core i7/Linux内存系统

### 1.7.1 地址翻译

<center><img src="image-20191125120512021.png" alt="image-20191125120512021" style="zoom:50%;" /></center>


### 1.7.2 Linux虚拟内存系统

<center><img src="image-20191125120939273.png" alt="image-20191125120939273" style="zoom:50%;" /></center>

# 2. 按需调页

程序开始时载入整个程序 VS 在需要时才调入相应的页

emmmm, vote for 按需调页

<br/>

按需调页的性能

内存访问时间为$ma$, 设p为page fault的概率[$0\leq p \leq 1$]

有效访问时间$=(1-p)\times ma + p\times$page fault时间

<br/>



# 3. 页面置换

页面置换的步骤

* 查找所需页在磁盘上的位置
* 查找一个空闲帧
  * 如果有空闲帧，就使用它
  * 如果没有空闲帧，就使用页置换算法来选择一个牺牲帧[victim frame]
  * 将牺牲帧的内容写到磁盘上，改变页表和帧表[用dirty bit来控制是否写回]
* 将所需页读入新空闲帧，改变页表和帧表
* 重启用户进程

<center><img src="image-20191202135842164.png" alt="image-20191202135842164" style="zoom: 40%;" /></center>


## 3.1 FIFO

<center><img src="image-20191202140215811.png" alt="image-20191202140215811" style="zoom:50%;" /></center>


FIFO : 0虽然最近访问过，但是是first in的，***FIFO的first in是只看谁最先进入memory***

<br/>

## 3.2 最优置换

开天眼，可以预测未来

置换在将来最晚被使用的页

<center><img src="image-20191202154701416.png" alt="image-20191202154701416" style="zoom:50%;" /></center>

<br/>

## 3.3 LRU页置换

选择内存中最久没有引用的页面被置换。

这是局部性原理的合理近似，性能接近最佳算法

但是由于需要记录页面使用时间，硬件开销太大

<center><img src="image-20191202155058918.png" alt="image-20191202155058918" style="zoom:50%;" /></center>

<br/>

实现方式：

* 计数器: 每个页表关联一个使用时间域，并未CPU增加一个逻辑时钟或计数器。

  对每次内存引用，计数器都会增加，时钟寄存器的内容被复制到对应页表项的使用时间域内。

  This scheme requires 

  * a search of the page table to find the LRU page and a write to memory [to the time-of-use field in the page table] for each memory access. 
  * The times must also be maintained when page tables are changed [due to CPU scheduling]. 
  * Overflow of the clock must be considered.

* 栈：keep a stack of page numbers. Whenever a page is referenced, it is removed from the stack and put on the top. 这样，most recently used page总是在top, least recently used page 总是在bottom。

  可以实现为具有头尾指针的双向链表

<br/>

## 3.4 LRU-Approximation Page Replacement

页表内的每个page table entry 都关联着一个引用位[reference bit]。每当引用一个页时[无论读写], 相应的page table entry的引用位就被置位

### A. Additional-Reference-Bits Algorithm

We can keep an 8-bit byte for each page in a table in memory.用来存放8个时钟周期的引用位

* 在规定时间间隔内，时钟定时器产生中断并将控制权转交给操作系统。
* 操作系统把每个页的引用位转移到8bits的最高位，其他位右移并抛弃最低位

比如：

> 如果移位寄存器内容是00000000，说明这个页在最近8个周期没有被使用过
>
> 如果移位寄存器内容是11111111，说明这个页在最近8个周期都被至少使用过一次
>
> 具有值11000100的移位寄存器的页比具有01110111的页更为最近被使用

如果将这8个bits作为无符号整数，那么具有最小值的页就是least recently used

<center><video id="video" controls="" preload="none" width="400"><source id="mp4" src="https://miaochenlu.github.io/2020/09/17/MemoryManagement/referencebit.mov"> 
      <p>Your user agent does not support the HTML5 Video element.</p>
</video></center>

### B. 二次机会算法

为了避免FIFO可能会把经常使用的页替换出去的问题，我们可以对它做一个简单的修改：对最老页面的R位进行检查。

* 如果是0，那么这个页既老又没用，应该被立刻替换掉
* 如果是1，就清除这个位，把这个页放到页链表的尾端，修改它的装入时间让它就象刚装入的一样，然后继续搜索下一个FIFO页。

<center><video id="video" controls="" preload="none" width="400"><source id="mp4" src="https://miaochenlu.github.io/2020/09/17/MemoryManagement/secondchance.mov"> 
      <p>Your user agent does not support the HTML5 Video element.</p>
</video></center>




<a href="http://netclass.csu.edu.cn/NCourse/hep086/chapter4/section4/4.4.4.htm">second chance</a>

<center><video id="video" controls="" preload="none" width="400"><source id="mp4" src="https://miaochenlu.github.io/2020/09/17/MemoryManagement/clockreplace.mov"
      <p>Your user agent does not support the HTML5 Video element.</p>
</video></center>


### C. 增强型二次机会算法

考虑到替换脏页需要将页从内存写回磁盘，所以脏页并不是比较好的替换选择

考虑[引用位, 修改位]的有序对

* [0, 0]最近既没有使用页没有修改---置换的最佳页
* [0, 1]最近没有使用但是被修改过---不是很好，因为被置换需要写到磁盘
* [1, 0]最近使用过但是没有修改---他有可能很快又要被使用
* [1, 1]最近使用过并且修改过---他又可能很快又要被使用，并且置换之前需要将页写回磁盘

每个有序对都代表了一个类，[0,0]是最小的类，[1, 0]类的页在它的R位被时钟中断清除后就成了[0,0]类。

<center><video id="video" controls="" preload="none" width="400"><source id="mp4" src="https://miaochenlu.github.io/2020/09/17/MemoryManagement/advancesecondchance.mov"> 
      <p>Your user agent does not support the HTML5 Video element.</p>
</video></center>


从编号最小的非空类中挑选出一个页淘汰。注意在找到置换页之前，可能需要多次搜索整个循环队列



## 3.5 页缓冲算法？？？？？

通过被置换页面的缓冲，有机会找回刚被置换的页面

* 被置换页面的选择和处理：用FIFO算法选择被置换页，把被置换的页面放入两个链表之一。即：如果页面未被修改，就将其归入到空闲页面链表的末尾，否则将其归入到已修改页面链表。

* 需要调入新的页面时，将新页面内容读入到空闲页面链表的第一项所指的页面，然后将第一项删除。

* 空闲页面和已修改页面，仍停留在内存中一段时间，如果这些页面被再次访问，这些页面还在内存中。

* 当已修改页面达到一定数目后，再将它们一起调出到外存，然后将它们归入空闲页面链表。



<br/>

# 4. 帧分配

如何在各个进程之间分配一定的空闲内存



## 4.1 帧的最少数量

分配的帧不能超过可用帧的数量[除 非有页共享], 页必须分配至少最少数量的帧。

为什么要定最少帧数

> * 性能: 随着分配给每个进程的帧数量的减少，页错误会增加，从而减慢进程的执行。

每个进程帧的最少数量是由体系结构决定的，而最大数量是由可用物理内存的数量来决定。

<br/>

## 4.2 分配算法

* 平均分配

  在n个进程之间分配m个帧，每个进程获得$\frac{m}{n}$帧

* 比例分配

  各个进程需要不同数量的内存。

  根据进程大小，将可用内存分配给每个进程。

  设进程$p_i$的虚拟内存大小为$s_i$,定义$S=\sum s_i$

  如果可用帧的数量为m,那么进程$p_i$可以分配到$ a_i$,   $a_i\approx \frac{s_i}{S}\times m$

  调整$a_i$使得，最小数量$<a_i<$m

<br/>

## 4.3 全局分配和局部分配

* ### 全局置换

> 允许一个进程从所有帧集合中选择一个置换帧，不管这个帧是否已经分配给其他进程

例如：允许高优先级进程从低优先级进程中选择帧以便置换。这种方法允许高优先级进程增加其帧分配而以损失低优先级进程为代价。

优点：

> 全局置换会有更好的系统吞吐量，且更为常用

问题：

> 进程不能控制其page fault率。一个进程的位于内存的页集合不但取决于该进程本身的调页行为，还取决于其他进程的调页行为。

* ### 局部置换

> 进程仅从自己的分配帧中进行选择

优点：

> page fault数量可控

问题：

> 局部置换不能使用其他进程不常用的内粗，所以会阻碍一个进程。

<br/>

# 5. 系统颠簸[Thrashing]

If a process does not have “enough” pages, the page-fault rate is very high. This leads to:

* 很低的CPU利用率

* 误导OS以为有必要提高多任务的程度

* 误导OS装入更多作业，内存中驻留更多进程

* 于是，每个进程拥有的页帧数更少

thrashing是page频繁的换入换出操作, 将导致严重的性能问题



<center><img src="image-20191205135228155.png" alt="image-20191205135228155" style="zoom:50%;" /></center>

<br/>

## 5.1 Working-set model

working-set model基于局部性的假设

$\Delta$定义为working set window的大小。

检查最近$\Delta$个页的引用。这最近$\Delta$个引用的页集合称为working set

<br/>

如果一个页正在使用中，那么它就在工作集合中。

如果这个页不再使用，那么它会在其上次引用的$\Delta$时间单位后从working set中删除。

也就是说，working set是程序局部的近似

<center><img src="image-20191205140609132.png" alt="image-20191205140609132" style="zoom:50%;" /></center>

> 举个例子, $\Delta=10$, 那么t1时刻的working set就是{1, 2, 5, 6, 7}
>
> t2时刻的working set就是{3, 4}



考虑每个进程工作集的大小，如果每个进程的工作集合为$WSS_i$, D是总的帧需求量

$$D=\sum WSS_i$$

如果D>m，那么就有的进程得不到足够的帧，就会出现颠簸



如何使用working-set model来handle thrashing呢？

> 确定了$\Delta$后，操作系统跟踪每个进程的工作集合，并为进程分配大于其工作集合的帧数
>
> * 如果还有空闲帧，则可以启动另一个进程。
> * 如果所有working set之和的增加超过了可用帧的总数，那么操作系统会选择暂停一个进程，该进程的页被写出，他的帧可以分配给其他进程，挂起的进程可以在以后重启。



## 5.2 page fault frequency

working set来控制thrashing并不灵活，一种更加直接的方法是页错误频率[page fault frequency, PFF]

当PFF太高时，说明进程需要更多帧，如果PFF太低，说明进程有太多帧。可以为PFF设置上限和下限。

* 如果PFF超过上限，可以为进程分配更多的帧。如果没有可用帧，那么必须选择一个进程暂停，并将释放的帧分配给那些具有高PFF的进程

* 如果PFF低于下限，可以从进程中移走帧。

可以直接测量和控制PFF来防止颠簸

<center><img src="image-20191205142402546.png" alt="image-20191205142402546" style="zoom:45%;" /></center>



