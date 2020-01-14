---
layout: article
title: PPL-subprogram
tags: PPL
key: page-PPLsubprogram
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
  background_image:
    src: https://miaochenlu.github.io/picture/EDD6F47E7DDE25D08BB6320511A6F0BD.png
---

principles of programming language--子程序和控制抽象

<!--more-->

<style>
  .page__header .header__brand path {
    fill: rgba(255, 255, 255, .95);
  }
</style>

<br/>



什么是抽象：

> 抽象是一种过程，程序员通过抽象将一个名字与一段可能很复杂的程序片段关联起来，然后可以从相关的用途或者功能的角度来考虑它，而不是从具体的实现来考虑它

抽象可以分为

* 控制抽象：定义一个良好的操作
* 数据抽象：表示信息

子程序是最主要的控制抽象机制。

子程序能够代表调用方来执行它的操作，调用方在继续自己的执行前等待子程序结束。

大多数子程序都是参数化的，即调用方传递一些参数来影响子程序的行为，或为其操作提供数据。这种参数也称为实参，在调用发生的时刻，他们被映射到子程序的形参。

返回值的子程序通常被称为函数，不返回值的子程序通常被称为过程。

<br/>



# 1 静态分配

requirements

* simple subprograms
  * subprograms cannot be ***nested***
  * all local variables are ***static***
* required storage for calls and returns
  * status information of caller, parameters, return address, return value for functions
* A simple subprogram consists of two parts
  * subprogram code
  * ***node-code part***(local variables and above data for calls and returns)

<br/>  

***Activation record:***

> Format, or layout, of non-code part of an executing subprogram
>
> 包含参数、返回值、局部变量、临时量和一些簿记信息

For a simple subprogram, AR has fixed size, and can be ***statically allocated*** [not in stack]

这样是不能支持递归的

<center><img src="https://miaochenlu.github.io/picture/image-20191127111726393.png" alt="image-20191127111726393" style="zoom: 33%;" /></center>
<table>
  <tr>
    <td><img src="https://miaochenlu.github.io/picture/image-20191127114939416.png" style="zoom: 33%"; /></td>
    <td><p>
      Code and activation records of a program with 3 simple subprograms:A, B, C.
      </p>
      <p>
        These parts mey be separately compiled and put together by linker
      </p></td>
  </tr>
</table>



Code and activation records of a program with 3 simple subprograms:A, B, C.These parts mey be separately compiled and put together by linker

<br/>

​     

# 2. Implementing Subprograms with Stack-Dynamic Local Variables

这样做的一个好处就是： 支持递归

缺点： More complex storage management

* compiler must generate code for ***implicit allocation and deallocation*** of local variables on the stack
* Recursion adds possibility of multiple simultaneous activations of a subprogram. That is "***multiple instances of activations records***".

<br/>



<center><img src="https://miaochenlu.github.io/picture/image-20191127115905686.png" alt="image-20191127115905686" style="zoom:35%;" /></center>
<br/>

看一个例子

### i. content of activation record

```cpp
int AddTwo(int x, int y) {
  int sum;
  sum = x + y;
  return sum;
}
```

<center><img src="https://miaochenlu.github.io/picture/image-20191127120114207.png" alt="image-20191127120114207" style="zoom: 30%;" /></center>
<br/>

### ii. accessing activation record

When `AddTwo` is called, its AR is dynamically created and pushed onto the run-time stack

<br/>

#### 如下图，怎么去寻找stack中的变量呢，比如x,y,sum?

<table>
  <tr>
    <td><center><img src="https://miaochenlu.github.io/picture/image-20191127120644756.png" alt="image-20191127120644756" style="zoom:40%;" /></center></td>
    <td><img src="https://miaochenlu.github.io/picture/image-20191127121052887.png" alt="image-20191127121052887" style="zoom:40%;" /></td>
  </tr>
</table>



#### Idea: use addresses relative to a base address of AR, which does not change during subprog. 

vote for base pointer! Dedicate a register to hold this pointer --> ***BP*** 

Base pointer(BP)

> * Always points at the ***base of the activation record instance*** of the currently executing program unit 
> * When a subprogram is called, the ***current BP is saved in the new AR instance***[dynamic link] and the BP is set to point at the base of the new AR instance 
> * Upon return from the subprogram , BP is restored from the AR instance of the callee

这样就可以寻址了，比如BP+8

<center><img src="https://miaochenlu.github.io/picture/image-20191127121121729.png" alt="image-20191127121121729" style="zoom: 33%;" /></center>
<br/>

<center><img src="https://miaochenlu.github.io/picture/image-20191127121808159.png" alt="image-20191127121808159" style="zoom: 25%;" /></center>
<br/>

<center><img src="https://miaochenlu.github.io/picture/截屏2019-11-27下午12.18.38的副本.png" alt="截屏2019-11-27下午12.18.38的副本" style="zoom: 25%;" /></center>

#### 看一下一个没有递归的例子

<center><img src="https://miaochenlu.github.io/picture/image-20191127123111611.png" alt="image-20191127123111611" style="zoom: 25%;" /></center>
<br/>

<center><img src="https://miaochenlu.github.io/picture/image-20191127123149311.png" alt="image-20191127123149311" style="zoom:25%;" /></center>
static link

> 在那些允许嵌套子程序和静态作用域的语言中，对象有可能出现在外围的子程序只能够，通过维护一个静态链，就可以找到这些既非局部也非全局的对象。每个AR都包含一个词法上位于其外围的帧的引用

dynamic link

> 为在子程序返回时恢复而保存的base pointer称为动态链指针



#### 看一下一个递归的例子

代码

<center><img src="https://miaochenlu.github.io/picture/image-20191127124138451.png" alt="image-20191127124138451" style="zoom: 25%;" /></center>
调用时

<center><img src="https://miaochenlu.github.io/picture/image-20191127124225391.png" alt="image-20191127124225391" style="zoom: 40%;" /></center>
调用返回时

<center><img src="https://miaochenlu.github.io/picture/image-20191127124238076.png" alt="image-20191127124238076" style="zoom:40%;" /></center>
# 4. Nested subprograms

<center><img src="https://miaochenlu.github.io/picture/image-20191127124749204.png" alt="image-20191127124749204" style="zoom:50%;" /></center>
<table>
  <tr>
    <td><img src="https://miaochenlu.github.io/picture/image-20191127125214414.png" alt="image-20191127125214414" style="zoom:40%;" /></td>
    <td><img src="https://miaochenlu.github.io/picture/image-20191127125249115.png" alt="image-20191127125249115" style="zoom:40%;" /></td>
  </tr>
</table>

