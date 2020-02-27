---
title: "Numerical Analysis-Lecture1"
tags: NA
key: page-NA1


---

<!--more-->

# 1. some review

* Taylor's Theorem

Suppose $f\in C^n[a,b]$, that $f^{(n+1)}$ exists on $[a, b]$, and $x_0 \in [a, b]$. For every $x \in [a, b]$, there exists a number $\xi(x)$ between $x_0$ and $x$ with

$$f(x)=P_n(x)+R_n(x)$$

where

$$P_n(x)=f(x_0)+f'(x_0)(x-x_0)+\frac{f''(x_0)}{2!}(x-x_0)^2+\cdots+\frac{f^{(n)}(x)}{n!}$$

$$=\underset{k=0}{\overset{n}{\sum}}\frac{f^{(k)}(x_0)}{k!}(x-x_0)^k$$

and

$$R_n(x)=\frac{f^{(n+1)}(\xi(x))}{(n+1)!}(x-x_0)^{n+1}$$

> $P_n(x)$ is called the **nth Taylor polynomial** for f about $x_0$
>
> $R_n(x)$ is called the ***remainder term(or truncation error)***

<a href="https://www.zhihu.com/question/21149770?sort=created">taylor</a>



See an example

> Let $f(x)=cosx$ and $x_0=0$. Determine
>
> * the second Taylor polynomial for $f$ about $x_0$; 
>
> $\begin{aligned}cosx &=f(0)+f'(0)x+\frac{1}{2!}f''(x)x^2+\frac{1}{3!}f'''(\xi(x))x^3\\=&1-\frac{1}{2}x^2+\frac{1}{6}x^3sin\xi(x)\end{aligned}$
>
> $0<\xi(x)<x$
>
> 当$x=0.01$时，来估算$cosx$
>
> $\begin{aligned}cos0.01 &=1-\frac{1}{2}0.01^2+\frac{1}{6}x^3sin\xi(0.01)\\&=0.99995+\frac{10^{-6}}{6}sin\xi(0.01)\end{aligned}$
>
> $\vert cos0.01-0.99995\vert=0.1\bar{6}\times 10^{-6}\vert sin\xi(0.01)\vert\leq 0.1\bar{6}\times 10^{-6}$
>
> 



# 1.2 Round-off Errors and Computer Arithmetic

## A. Error types

* truncation error: The error involved in using a truncated, or finite, summation to approximate the sum of an infinite series. 像泰勒展开中的Rn

* round-off error: The error produced when performing real number calculations. It occurs because the arithmetic performed in a machine involves numbers with only a finite number of digits.



## B. Binary Machine Numbers

Floating point representation

64-bit binary digit = 1 bit s(sign indicator) + 11-bit exponent c(characteristic) + 52-bit binary fraction f(mantissa)

$$(-1)^s2^{c-1023}(1+f)$$



## C. Decimal Machine Numbers

normalized decimal floating-point form of a real number:

$\pm 0.d_1d_2\cdots d_k\times 10^n$ where $1\leq d_1\leq 9$ and $0\leq d_i\leq 9(i=2,\cdots,k)$

Given a real number $y=0.d_1d_2\cdots d_kd_{k+1}d_{k+2}\times 10^n$

the floating-point form of y, denoted $fl(y)$

* Chopping---> simply chop off the digits $d_{k+1}d_{k+2}\cdots$

$fl(y)=0.d_1d_2\cdots d_k\times 10^n$ 

* Rounding---> add $5\times 10^{n-(k+1)}$ to y and then chops the result to obtain a number of the form

$$fl(y)=0.\delta_1\delta_2\cdots\delta_k\times 10^n$$

