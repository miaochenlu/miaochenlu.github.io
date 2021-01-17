---
title: cache_hit_miss_threshold
date: 2021-01-06 12:19:57
tags:
hide: true
---



# inline assembly

在这里我们会使用到内联汇编，也就是嵌入到C/C++中到汇编代码。GCC采用的是AT&T汇编

GCC内联汇编的格式如下所示

```assembly
asm ( 汇编语句
    : 输出操作数     // 非必需
    : 输入操作数     // 非必需
    : 其他被污染的寄存器 // 非必需
    );
```

* 第一行是汇编语句，需要用双引号引起来；如果有多条语句，需要用";"或者"\n\t"来分隔

* 第二行是输出操作数，形式为"=type(var)"

&emsp;&emsp; 其中var可以是任意内存变量，最终结果会存放到这个变量中

&emsp;&emsp; type一般是如下标识符，表明了汇编中用什么来存这个操作数

a,b,c,d,S,D 分别代表 eax,ebx,ecx,edx,esi,edi 寄存器 

r 上面的寄存器的任意一个（谁闲着就用谁） 

m 内存 

i 立即数（常量，只用于输入操作数） 

g 寄存器、内存、立即数 都行

**在汇编中用 %序号 来代表这些输入/输出操作数，序号从 0 开始。为了与操作数区分开来，寄存器用两个%引出，如：%%eax**		

* 第三行是输入操作数。形式为"type(var)"。type可以是上述标识符，也可以是输出操作数的序号(如%0)，用var来初始化这个操作数。
* 第4行标出那些在汇编代码中修改了的、又没有在输入/输出列表中列出的寄存器，这样 gcc 就不会擅自使用这些"危险的"寄存器。还可以用 "memory" 表示在内联汇编中修改了内存，之前缓存在寄存器中的内存变量需要重新读取。

下面来看个例子

```c
#include <stdio.h>

int main() {
  int a = 1, b = 2, c = 0;

  asm(
    "addl %2, %1"
    : "=g"(c), "=g"(b)
    :  "0"(a)
    : "memory"
      );
  printf("now a: %d  b: %d  c: %d\n", a, b, c);
}
```

```
now a: 1  b: 3  c: 1
```

c对应%0，b对应%1, a对应%2

`"0"(a)`用a初始化%0, 因此最终c=1

`addl %2, %1` %1=%2+%1, 最终b=3



# Time Stamp Counter--obtain timing information

Time Stamp Counter (TSC)是x86处理器中的一个64位寄存器。这个寄存器中记录了从reset到当下的clock cycle。

如何获取tsc的值？

`rdtsc`是一条读取tsc寄存器的指令。这个值会以如下形式存放`edx:eax`，也就是将tsc的低32位放在`eax`寄存器中, 将tsc的高32位放在`edx`寄存器中

在汇编中可以如此使用

```assembly
asm volatile("rdtsc" : "=a" (low), "=d" (high))
```



在c语言中可以写出如下代码

```c
uint64_t a, d;
asm volatile ("rdtsc" : "=a" (a), "=d" (d));
a = (d<<32) | a;
```

这样a中就存储了当前的clock cycle



rdtsc要和memory fence配合使用

lfence

mfence会清空store buffer become globally visible





lfence: https://hadibrais.wordpress.com/2018/05/14/the-significance-of-the-x86-lfence-instruction/#:~:text=The%20SFENCE%2C%20LFENCE%2C%20and%20MFENCE,Manual%20Volume%203%20Section%208.2.&text=One%20important%20phrase%20here%20is%20%E2%80%9Cweakly%2Dordered%20results%E2%80%9D.

sfence: https://hadibrais.wordpress.com/2019/02/26/the-significance-of-the-x86-sfence-instruction/

https://stackoverflow.com/questions/27627969/why-is-or-isnt-sfence-lfence-equivalent-to-mfence

https://stackoverflow.com/questions/27595595/when-are-x86-lfence-sfence-and-mfence-instructions-required

* rdtsc
* clflush
* 





# Reference

[1] https://blog.csdn.net/liuqiaoyu080512/article/details/8457528?utm_medium=distribute.pc_relevant_download.none-task-blog-BlogCommendFromBaidu-10.nonecase&depth_1-utm_source=distribute.pc_relevant_download.none-task-blog-BlogCommendFromBaidu-10.nonecas

[2] https://blog.csdn.net/slvher/article/details/8864996?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1.control

[3] https://blog.csdn.net/gaotangtiankai/article/details/19410509?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-2.control

[4] https://blog.csdn.net/gonxi/article/details/6104842?utm_medium=distribute.pc_relevant.none-task-blog-searchFromBaidu-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-searchFromBaidu-2.control