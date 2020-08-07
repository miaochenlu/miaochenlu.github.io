---
title: CA - Instruction Set
date: 2019-09-08 22:04:07
tags: Computer Architecture
categories: Computer Architecture
index_img: /img/image-20190930101228492.png
---

# <a name="ISA">附录A 指令集基本原理 </a>

# 1 What is ISA?

ISA: Instruction Set Architecture

<img src="image-20190930101228492.png" alt="image-20190930101228492" style="zoom:50%;" />

# 2 What types of ISA?

### 2.1 Basis

* type of internal storage

> stack
>
> accumulator
>
> Register-memory
>
> Register-register/load-store

<br/>

先来解释一下implicit operand和explicit operand

* explicit operand

明确到哪个地方去取的操作数，比如到某一个由指令确定的***寄存器***，或者内存中一个由指令确定的***存储器地址***

* implicit operand

不明确的取值位置，但是系统默认了。比如stack architecture就是默认到stack头部去取，accumulator architecture就是默认到accumulator中去取。

<br/>

<img src="image-20191218224444341.png" alt="image-20191218224444341" style="zoom:50%;" />

C=A+B的代码示例

<img src="image-20191218224500000.png" alt="image-20191218224500000" style="zoom:50%;" />

example problem

<img src="image-20191218224924929.png" alt="image-20191218224924929" style="zoom:50%;" />

#### 2.1.1 Stack Architecture

***<u>operand</u>***:

>  2 ***<u>implicit</u>*** operands on the top of the stack(***TOS***)

用图来看一下以下操作的过程

```
C = A + B (memory locations)
Push A
Push B
Add 
Pop C
```



 <table>
   <tr>
     <td>
       <img src="image-20190930102555608.png" alt="image-20190930102555608" title="original"/>
     </td>
     <td>
  <img src="image-20190930102855084.png" alt="image-20190930102855084" title="push A"/>
     </td>
     <td>
  <img src="image-20190930103000391.png" alt="image-20190930103000391" title="push B"  />
     </td>
     <td>
  <img src="image-20190930103037853.png" alt="image-20190930103037853" title="Add"  />
     </td>
     <td>
  <img src="image-20190930103101742.png" alt="image-20190930103101742" title="pop C" />
     </td>
   </tr>
 </table>




#### 2.1.2 Accumulator Architecture

***<u>operand</u>***

> one implicit operand: accumulator
>
> one explicit operand: mem location

看一下以下操作的过程

```
C = A + B
Load A
Add B
Store C

accumulator is both 
an implicit input operand 
and a result
```

  

<table>
  <tr>
    <td>
      <img src="image-20190930115553486.png" alt="image-20190930115553486" title="original"/>
    </td>
    <td>
  <img src="image-20190930115635931.png" alt="image-20190930115635931" title="load A"/>
    </td>
  	<td>
  <img src="image-20190930115734010.png" alt="image-20190930115734010" title="add B"/>
    </td>
    <td>
  <img src="image-20190930115804883.png" alt="image-20190930115804883" title="store C"/>
    </td>
  </tr>
</table>




# 3. 存储器寻址

## 3.1 解释存储器地址

i. 关于如何对一个较大对象中的字节排序：

* Little Endian : store least significant byte in the smallest address

<img src="image-20191014101250484.png" alt="image-20191014101250484" style="zoom:40%;" />


* Big Endian : store most significant byte in the smallest address

<img src="image-20191014101429477.png" alt="image-20191014101429477" style="zoom:40%;" />

ii. 字节对齐

大小为s bytes的对象，字节地址为A, 如果$A\; mod\;s = 0$ , 那么是字节对齐的

<img src="image-20191014101703881.png" alt="image-20191014101703881" style="zoom:40%;" />

为什么要对齐

> When well aligned, requires only one memory access to read one object;
>
> If address is not well aligned, each misaligned object requires two memory accesses to fetch.

## 3.2  Addressing modes

<img src="image-20191014102744097-6809392.png" alt="image-20191014102744097-6809392" style="zoom:50%;" />





References:  

[1]计算机体系结构 量化研究方法

