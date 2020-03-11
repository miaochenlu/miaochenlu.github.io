# Solutions of Equations in One Variable -- Find a root of f(x)=0

# 1. The Bisection Method

### Theorem: (Intermediate Value Theorem)

If $f\in C[a,b]$ and K is any number between $f(a)$ and $f(b)$, then there exists a number $p\in(a,b)$ for which $f(p)=K$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302180701643.png" alt="image-20200302180701643" style="zoom:50%;" />

### Algorithm

To ﬁnd a solution to f (x) = 0 given the continuous function f on the interval [a, b], where f (a) and f (b) have opposite signs:

```cpp
Step1 Set i = 1;
			FA = f(a).
Step2 While i <= N0 do steps 3-6
  		Step3 Set p = a + (b - a) / 2;
								FP = f(p).
      Step4 If FP = 0 or (b - a) / 2 < tolerance then
                OUTPUT(p);
								STOP.
      Step5 Set i = i + 1.
      Step6 If sign(FA) * sign(FP) > 0 then set a = p;
																FA = FP
                           else set b = p.
      Step7 OUTPUT ('Method failed after N0 iterations, N0 =', N0 ); 								(The procedure was unsuccessful.)
						STOP.
```



When to stop?

我们不知道真实值是多少，因此只能估计和上一次解之间的差距

* $\vert p_N-p_{N-1}\vert<\epsilon$

* $\frac{\vert p_N-p_{N-1}\vert}{\vert p_N\vert}<\epsilon,\quad p_N\not=0$

* $\vert f(p_N)\vert <\epsilon$

> 但是存在这样一种情况
>
> 虽然$\vert p_N-p_{N-1}\vert<\epsilon$, 但是数列$\{p_N\}$发散， 如下图所示，$\vert f(p_N)\vert \leq \epsilon$, 但是$p_N$和真实解相差很远

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302182506576.png" alt="image-20200302182506576" style="zoom:50%;" />

但是根据二分这种方法，我们依然可以将误差限定在一个范围内，看以下定理

Suppose that $f\in C[a,b]$ and $f(a)\cdot f(b)<0$. The Bisection method generates a sequence $\{p_n\}(n=0,1,2,\cdots)$ approximating a zero $p$ of $f$ with

$$\vert p_n-p\vert \leq \frac{b-a}{2^n}$$

这个定理还是比较好理解的，每次二分，范围就缩小一半

<br/>

### Comments

pros

> * simple, only requires a continuous $f$ (没有要求可导)
> * Always converges to a solution

cons

> * Slow to converge, and a good intermediate approximation can be inadvertently discarded
> * cannot find multiple roots and complex roots

Pratically

we can **sketch a graph** of $f(x)$ before applying the bisection method. 

Or use a subroutine to **partition the interva**l into several sub-intervals $[a_k, b_k]$ so that $f(a_k)\cdot f(b_k)<0$ even when $f(a)\cdot f(b)>0$







Find an approximation to $\sqrt[3]{25}$ correct to within $10^{-4}$ using the Bisection Algorithm

> $f(x)=x^3-25$
>
> $2^3=8, 3^3=27\Rightarrow 2<\sqrt[3]{25}<3$
>
> $10^4\approx=2^{14}$

| n    | $a_n$       | $b_n$        | $p_n$        | $f(p_n)$         |
| ---- | ----------- | ------------ | ------------ | ---------------- |
| 1    | 2           | 3            | 1.5          | -21.628          |
| 2    | 1.5         | 3            | 2.25         | -13.609375       |
| 3    | 2.25        | 3            | 2.625        | -6.912109375     |
| 4    | 2.625       | 3            | 2.8125       | -2.7526855469    |
| 5    | 2.8125      | 3            | 2.90625      | -0.4529724121    |
| 6    | 2.90625     | 3            | 2.953125     | 0.7540473938     |
| 7    | 2.90625     | 2.953125     | 2.9296875    | 0.1457095146     |
| 8    | 2.90625     | 2.9296875    | 2.91796875   | -0.1548336148    |
| 9    | 2.91796875  | 2.9296875    | 2.923828125  | -0.004863195121  |
| 10   | 2.923828125 | 2.9296875    | 2.9267578125 | 0.07034779806    |
| 11   | 2.923828125 | 2.9267578125 | 2.9252929688 | 0.03272347176    |
| 12   | 2.923828125 | 2.9252929688 | 2.9245605469 | 0.01392543175    |
| 13   | 2.923828125 | 2.9245605469 | 2.924194336  | 0.004529943101   |
| 14   | 2.923828125 | 2.924194336  | 2.9240112305 | -0.0001669201155 |

Use Theorem 2.1 to ﬁnd a bound for the number of iterations needed to achieve an approximation with accuracy $10^{−4}$ to the solution of $x^3 −x−1 = 0$ lying in the interval [1, 2]. Find an approximation to the root with this degree of accuracy



# Fixed-point iteration

### What is fixed-point

A ﬁxed point for a function is a number at which the value of the function does not change when the function is applied. The number $p$ is fixed point for a given function $g$ if $g(p)=p$.

Start from an initial approximation $p_0$ and generate the sequence $\{p_n\}_{n=0}^{\infty}$ by letting $p_n=g(p_{n-1})$, for each $n\geq 1$. If the sequence converges to $p$ and $g$ is continuous, then 

$$p=\underset{n\rightarrow\infty}{lim}p_n=\underset{n\rightarrow\infty}{lim}g(p_{n-1})=g(\underset{n\rightarrow\infty}{lim}p_{n-1})=g(p)$$

<br/>

根和不动点存在等价性

$f(x)=0\Rightarrow x=g(x)$

$0=f(x)\Rightarrow x+0=x+f(x)\triangleq g(x)$



### algorithm

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302220736077.png" alt="image-20200302220736077" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200302221454857.png" alt="image-20200302221454857" style="zoom:50%;" />

这个方法不一定是收敛的

```cpp
Step 1  Set  i = 1;
Step 2  While ( i <= Nmax) do  steps 3-6
			 	Step 3  Set  p = g(p0);  /* compute pi */
				Step 4  If  | p - p0 | < TOL  then  Output (p);  /* successful */
									STOP;
        Step 5  Set  i++;
        Step 6  Set  p0 = p;   /* update p0 */
Step 7  Output (The method failed after Nmax iterations);  /* unsuccessful */
				STOP
```



### Theorem(Floating-Point Theorem)

定义：Let $g\in C[a,b]$ be such that $g(x)\in[a,b]$, for all $x$ in $[a,b]$. 

假设：Suppose, in addition, that $g'$ exists on $(a,b)$ and that a constant $0<k<1$ exists with $\vert g'(x)\vert\leq k$ for all $x\in(a,b)$. 

结论：Then, for any number $p_0$ in $[a,b]$, the sequence defined by $p_n=g(p_{n-1}), n\geq 1$, **converges** to the **unique** fixed point $p$ in $[a,b]$

<br>

**attention**

$$\vert g'\vert\leq k, 0<k<1$$

which does not include $\vert g'\vert\rightarrow 1_-$ , because k is a const

$$0\leq\vert g'\vert\leq1$$

which includes $\vert g'\vert\rightarrow 1_-$

<br>

Proof

<u>a. fixed point?</u>

Let $f(x)=g(x)-x$

$a\leq g(x)\leq b\Rightarrow f(a)=g(a)-a\geq 0\; and\; f(b)=g(b)-b\leq 0$

The **Intermediate Value Theorem** implies that $f$ has a root, and hence $g$ <u>has a fixed point</u>

<u>b. unique fixed point?</u>

Prove by contradiction:

Suppose $p$ and $q$ are both fixed points of $g$ in $[a,b]$ and $p\not= q$. The **Mean Value Theorem**  implies that a number $\xi$ exists between p and q with $g(p)-g(q)=g'(\xi)(p-q)\Rightarrow (1-g'(\xi))(p-q)=0\Rightarrow g'(\xi)=1$

Contradiction!!!

<u>c. converge?</u>

$$\underset{n\rightarrow\infty}{lim}\vert p_n-p\vert=0?$$

$g(x)\in [a.b]$, for all $x$ in $[a,b]\Rightarrow$ $p_n$ is defined for all $n\geq 0$

$\vert p_n-p\vert=\vert g(p_{n-1})-g(p)\vert=\vert g'(\xi)\vert\vert p_{n-1}-p\vert\leq k\vert p_{n-1}-p\vert\leq k^2\vert p_{n-2}-p\vert\leq\cdots\leq k^n\vert p_0-p\vert\rightarrow 0$

<br>

#### Corollary

If $g$ satisfies the hypotheses of the Fixed-Point Theorem, then bounds for the error involved in using $p_n$ to approximate $p$ are given by (for all $n\geq 1$)

$$\vert p_n-p\vert\leq\frac{1}{1-k}\vert p_{n+1}-p_n\vert\quad and\quad \vert p_n-p\vert\leq\frac{k^n}{1-k}\vert p_1-p_0\vert$$

Proof

$\vert p_{n+1}-p_n\vert\geq \vert p_n-p\vert-\vert p_{n+1}-p\vert\geq\vert p_n-p\vert-k\vert p_n-p\vert$

$\Rightarrow \vert p_n-p\vert\leq\frac{1}{1-k}\vert p_{n+1}-p_n\vert$

$\vert p_{n+1}-p_n\vert=\vert g(p_n)-g(p_{n-1})\vert=\vert g'(\xi)\vert\vert(p_n-p_{n-1})\vert\leq k\vert p_n-p_{n-1}\vert\leq k^n\vert p_1-p_0\vert$

$\Rightarrow (1-k)\vert p_n-p\vert\leq \vert p_{n+1}-p_n\vert\leq k^n\vert p_1-p_0\vert$

$\Rightarrow\vert p_n-p\vert\leq\frac{k^n}{1-k}\vert p_1-p_0\vert$



## 3. Newton's Mehod(Newton-Rsphson Method)

Let $p_0\in[a,b]$ be an approximation to $p$ such that $f'(p_0)\not=0$. Consider the first Taylor polynomial of $f(x)$ expanded about $p_0$

$$f(x)=f(p_0)+f'(p_0)(x-p_0)+\frac{f''(\xi_x)}{2!}(x-p_0)^2$$ 

$\xi_x$ lies between $p_0$ and $x$

Assume that $\vert p-p_0\vert$ is small, then $(p-p_0)^2$ is much smaller

$\Rightarrow 0=f(p)\approx f(p_0)+f'(p_0)(p-p_0)\Rightarrow p\approx p_0-\frac{f(p_0)}{f'(p_0)} $

<br>

迭代公式

$$p_n=p_{n-1}-\frac{f(p_{n-1})}{f'(p_{n-1})}, for\; n\geq1$$



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200303225514573.png" alt="image-20200303225514573" style="zoom:50%;" />



### Theorem

Let $f\in C^2[a,b]$. If $p\in[a,b]$ is such that $f(p)=0$ and $f'(p)\not= 0$, then there exists a $\delta>0$ such that Newton's method generates a sequence $\{p_n\} (n=1, 2,\cdots)$ converging to $p$ for any initial approximation $p_0\in[p-\delta, p+\delta]$

> Proof:
>
> $p_n=g(p_{n-1})$ for $n\geq 1$ with
>
> $$g(x)=x-\frac{f(x)}{f'(x)}$$
>
> 
>
> Question1: Is $g(x)$ continous in a neighborhood of $p$ ?
>
> Answer: Yes
>
> $f\in C^2[a,b]\rightarrow f'(x)\;continuous$ and $f'(p)\not=0\rightarrow f'(x)\not=0$ in a neighborhood of p
>
> <br>
>
> Question2: Is $g'(x)$  bounded by $0<k<1$ in a neighborhood of p?
>
> Answer: Yes
>
> $g'(x)=\frac{f(x)f''(x)}{[f(x)]^2}\rightarrow g'(p)=0$
>
> $f''(x)$ is continuous--> $g'(x)$ is small and is continuous in a neighborhood of p
>
> <br>
>
> Question3: Does $g(x)$ map $[p-\delta,p+\delta]$ into itself?
>
> $\vert g(x)-p\vert=\vert g(x)-g(p)\vert=\vert g'(\xi)\vert\vert x-p\vert\leq k\vert x-p\vert<\delta$
>
> 
>
> 
>
> 
>
> 





$$x_{n+1}=x_n-\frac{f(x_n)}{f'(x_n)}$$



$$\begin{aligned}f_1(x_1,x_2)&=0\\f_2(x_1,x_2)&=0\end{aligned}$$



$$\begin{aligned}f_1(x_1,x_2)=&f_1(x_{10},x_{20})+(x_1-x_{10})\frac{\partial f_1}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_1}{\partial x_2}(x_{10},x_{20})\\ +&\frac{1}{2!}(x_1-x_{10})^2\frac{\partial^2 f_1}{\partial x_1^2}(\xi_{x_1},\xi_{x_2})+\frac{1}{2!}(x_1-x_{10})(x_2-x_{20})\frac{\partial^2 f_1}{\partial x_1\partial x_2}(\xi_{x_1},\xi_{x_2})\\+&\frac{1}{2!}(x_1-x_{10})(x_2-x_{20})\frac{\partial^2 f_1}{\partial x_2\partial x_1}(\xi_{x_1},\xi_{x_2})+\frac{1}{2!}(x_2-x_{20})^2\frac{\partial^2 f_1}{\partial x_2^2}(\xi_{x_1},\xi_{x_2})\end{aligned}$$



$\xi_{x_2}$

$$\begin{aligned}=\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}x_1-x_{10}\\x_2-x_{20}\end{matrix}\right]\end{aligned}$$



$$=\left[\begin{matrix}x_1-x_{10} & x_2-x_{20}\end{matrix}\right]\left[\begin{matrix}\frac{\partial f_1^2}{\partial x_1^2} & \frac{\partial f_1^2}{\partial x_1x_2}\\ \frac{\partial f_1^2}{\partial x_1x_2}& \frac{\partial f_1^2}{\partial x_2^2}\end{matrix}\right] \left[\begin{matrix}x_1-x_{10} \\ x_2-x_{20}\end{matrix}\right]$$



$$\begin{aligned}f_2(x_1,x_2)=&f_2(x_{10},y_{10})+(x-x_{10})\frac{\partial f_2}{\partial x_1}(x_{10},x_{20})+(y-y_{10})\frac{\partial f_2}{\partial x_2}(x_{10},x_{20})\\ +&\frac{1}{2!}(x-x_{10})^2\frac{\partial^2 f_2}{\partial x_2^2}(x_{10},x_{20})+\frac{1}{2!}(x-x_{10})(y-y_{10})\frac{\partial^2 f_2}{\partial x_1\partial x_2}(x_{10},x_{20})\\+&\frac{1}{2!}(x-x_{10})(y-y_{10})\frac{\partial^2 f_2}{\partial x_2\partial x_1}(x_{10},x_{20})+\frac{1}{2!}(y-y_{10})^2\frac{\partial^2 f_2}{\partial x_2^2}(x_{10},x_{20})\end{aligned}$$





$$\begin{aligned} & f_1(x_1,x_2)\approx f_1(x_{10},y_{10})+(x_1-x_{10})\frac{\partial f_1}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_1}{\partial x_2}(x_{10},x_{20})\\ & f_2(x_1,x_2)\approx f_2(x_{10},y_{10})+(x_1-x_{10})\frac{\partial f_2}{\partial x_1}(x_{10},x_{20})+(x_2-x_{20})\frac{\partial f_2}{\partial x_2}(x_{10},x_{20})\end{aligned}$$



雅可比矩阵

$$\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]$$



$$\left[\begin{matrix}f_1 \\ f_2\end{matrix}\right]\approx \left[\begin{matrix}f_1 (a_0,b_0)\\ f_2(a_0,b_0)\end{matrix}\right]+\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}x_1-a_0\\x_2-b_0\end{matrix}\right]$$

黑塞矩阵



$0=\left[\begin{matrix}f_1(a,b) \\ f_2(a,b)\end{matrix}\right]\approx \left[\begin{matrix}f_1 (a_0,b_0)\\ f_2(a_0,b_0)\end{matrix}\right]+\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]\left[\begin{matrix}a-a_0\\b-b_0\end{matrix}\right]$







$$\left[\begin{matrix}a\\b\end{matrix}\right]\approx\left[\begin{matrix}a_0\\b_0\end{matrix}\right]-\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_0,b_0)\\f_2(a_0,b_0)\end{matrix}\right]$$



$$\left[\begin{matrix}a_n\\b_n\end{matrix}\right]=\left[\begin{matrix}a_{n-1}\\b_{n-1}\end{matrix}\right]-\left[\begin{matrix}\frac{\partial f_1}{\partial x_1} & \frac{\partial f_1}{\partial x_2} \\ \frac{\partial f_2}{\partial x_1}& \frac{\partial f_2}{\partial x_2}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_{n-1},b_{n-1})\\f_2(a_{n-1},b_{n-1})\end{matrix}\right]$$





$$\left[\begin{matrix}a_n\\b_n\end{matrix}\right]=\left[\begin{matrix}a_{n-1}\\b_{n-1}\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 2a_{n-1}& 2b_{n-1}\end{matrix}\right]^{-1}\left[\begin{matrix}f_1(a_{n-1},b_{n-1})\\f_2(a_{n-1},b_{n-1})\end{matrix}\right]$$



$$\left[\begin{matrix}a_0\\b_0\end{matrix}\right]=\left[\begin{matrix}0.8\\1.8\end{matrix}\right]$$

$$J=\left[\begin{matrix}1 & -1 \\ 2a_{n-1}& 2b_{n-1}\end{matrix}\right]=\left[\begin{matrix}1 & -1 \\1.6&3.6\end{matrix}\right]$$



$$\left[\begin{matrix}a_1\\b_1\end{matrix}\right]=\left[\begin{matrix}0.8\\1.8\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 1.6& 3.6\end{matrix}\right]^{-1}\left[\begin{matrix}0\\-0.12\end{matrix}\right]=\left[\begin{matrix}0.8230769\\ 1.8230769\end{matrix}\right]$$



$$\left[\begin{matrix}a_2\\b_2\end{matrix}\right]=\left[\begin{matrix}0.8230769\\ 1.8230769\end{matrix}\right]-\left[\begin{matrix}1 & -1 \\ 1.6461538 & 3.6461538 \end{matrix}\right]^{-1}\left[\begin{matrix}0\\0.0010651\end{matrix}\right]=\left[\begin{matrix}0.8228757\\ 1.8228757\end{matrix}\right]$$



$$\left[\begin{matrix}a_3\\b_3\end{matrix}\right]=\left[\begin{matrix}0.82287565553230 \\ 1.82287565553230 \end{matrix}\right]$$





$$\left[\begin{matrix}a\\b\end{matrix}\right]=\left[\begin{matrix}\frac{1}{2}(\sqrt7-1)\\ \frac{1}{2}(\sqrt7+1) \end{matrix}\right]$$















