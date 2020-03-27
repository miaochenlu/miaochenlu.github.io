# Iterative Techniques in Matrix Algebra

## 1. Norms or Vectors 

error of scalar

$x-x^*$

error of a set of variables $x=(x_1,x_2,\cdots,x_n)\in\mathbb{R}^n$

$x-x^*\in\mathbb{R}^n$



### A. Vector Norms

Definition: A vector norm on $\mathbb{R}^n$ is a function, $\Vert\cdot\Vert$ from $\mathbb{R}^n$ into $\mathbb{R}$ 

1. $\Vert \vec{x}\Vert\geq 0$ for all $\vec{x}in\mathbb{R}^n$
2. $\Vert \vec{x}\Vert=0$  if and only if $\vec{x}=0$
3. $\Vert\alpha \vec{x}\Vert=\vert\alpha\vert\Vert \vec{x}\Vert$ for all $\alpha\in\mathbb{R}$ and $\vec{x}\in\mathbb{R}^n$
4. $\Vert \vec{x}+\vec{y}\Vert\leq \Vert \vec{x}\Vert+\Vert \vec{y}\Vert$ for all $\vec{x},\vec{y}\in\mathbb{R}^n$

<br>

Some popular used norms:

$\Vert \vec{x}\Vert_1=\sum_{i=1}^n\vert x_i\vert$

$\Vert \vec{x}\Vert_2=\sqrt{\sum_{i=1}^n\vert x_i\vert^2}$

$\Vert \vec{x}\Vert_p=(\sum_{i=1}^n\vert x_i\vert^p)^{\frac{1}{p}}$

$\Vert \vec{x}\Vert_\infty=\underset{1\leq i\leq n}{max}\vert x_i\vert$

$\underset{p\rightarrow\infty}{lim}\Vert\vec{x}\Vert_p=\Vert \vec{x}\Vert_\infty$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200323143045024.png" alt="image-20200323143045024" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200323143057221.png" alt="image-20200323143057221" style="zoom:50%;" />

向量的收敛

Definition: A sequence $\{x^{(k)}\}_{k=1}^\infty$ of vectors in $\mathbb{R}^n$  is said to converge to $x$ with respect to the norm  $\Vert\cdot\Vert$  if, given any ε > 0, there exists an integer N(ε) such that

$$\Vert x^{(k)}-x\Vert\leq\varepsilon\quad for\;all\;k\geq N(\varepsilon)$$

<br>

Theorem: The sequence of vectors $\{x^{(k)}\}$ converges to $x$ in $\mathbb{R}^n$ with respect to the $l_\infty$ norm if and only if $\underset{k\rightarrow\infty}{lim} x_i^{(k)}=x_i$, for each $i=1,2\cdots,n$

Proof:

Suppose $\{x^{(k)}\}$ converges to $x$ with respect to the $l_\infty$ norm. Given any $\varepsilon>0$, there exists an integer $N(\varepsilon)$ such that for all $k\geq N(\varepsilon)$

$$\underset{i=1,2,\cdots,n}{max}\vert x_i^{(k)}-x_i\vert=\Vert x^{(k)}-x\Vert_\infty<\varepsilon$$







### B. Matrix Norms

Definition: A matrix norm on the set of all $n\times n$ matrices is a real-valued function, $\Vert\cdot\Vert$, defined on this set , satisfying for all $n\times n$ matrices A and B and all $\alpha\in C$



Natual Norm:

$\|A\|$是最大放大系数







## 3. Iterative Techniques

404