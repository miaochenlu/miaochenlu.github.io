# Initial-Value Problems for Ordinary Differential Equations



# 1. The Elementary Theory of Initial-Value Problems(IVP)

一阶常微分方程的初值问题

$$\begin{aligned}\frac{dy}{dx}&=f(t,y)&t\in[a,b]\\ y(a)&=\alpha \end{aligned}$$

Compute the approximations of $y(t)$ at a set of (usually equally-spaced) mesh points $a=t_0<t_1<\cdots<t_n=b$. 

也就是说，计算$w_i\approx y(t_i)=y_i,i=1,\cdots,n$

<br>

下面给出一些定义和定理

`definition`{:.warning}

**Lipschitz condition**: A function $f(t,y)$ is said to satisfy a Lipschitz condition in the variable $y$ on a set $D\subset R^2$ if a constant $L>0$ exists with

$$\vert f(t,y_1)-f(t,y_2)\vert \leq L\vert y_1-y_2\vert$$

Whenever $(t,y_1),(t,y_2)\in D$. The constant $L$ is called a **Lipschitz constant** for $f$. (相当于导数有界)

> 李普希兹连续就是说，一块地不仅没有河流什么的玩意儿阻隔，而且这块地上没有特别陡的坡。其中最陡的地方有多陡呢？这就是所谓的李普希兹常数。
> 悬崖的出现导致最陡的地方有“无穷陡”，所以不是李普希兹连续。
>
> <a href="https://www.zhihu.com/question/51809602">ref</a>

<br>

`Theorem`{:.error}

Suppose that $D=\{(t,y)\vert a\leq t\leq b,-\infty<y<\infty\}$ and that $f(t,y)$ is continuous on $D$. If $f$ satisfies a Lipschitz condition on $D$ in the variable $y$, then the IVP

$$y'(t)=f(t,y),a\leq t\leq b,y(a)=\alpha$$

has a unique solution $y(t)$ for $a\leq t\leq b$

<br>

`Definition`{:.warning}

The IVP:

$$y'(t)=f(t,y),a\leq t\leq b,y(a)=\alpha$$

is said to be a well-posed problem if:

1. 该问题存在唯一解$y(t)$

2. 一个小扰动对问题的解产生的影响也很小。

   For any $\varepsilon>0$, there exists a positive constant $k(\varepsilon)$, such that whenever $\vert\varepsilon_0\vert<\varepsilon$ and $\delta(t)$ is continuous with $\vert\delta(t)\vert<\varepsilon$ on $[a,b]$, a unique solution, $z(t)$, to

$$z'(t)=f(t,z)+\delta(t),a\leq t\leq b, z(a)=\alpha+\varepsilon_0$$

exists with $\vert z(t)-y(t)\vert <k(\varepsilon)\varepsilon$, for all $a\leq t\leq b$





# 2. Euler's Method

$$\begin{aligned}\frac{dy}{dx}&=f(t,y)&t\in[a,b]\\ y(a)&=\alpha \end{aligned}$$



Compute the approximations of $y(t)$ at a set of (usually equally-spaced) mesh points $a=t_0<t_1<\cdots <t_n=b$

<br>

$$y(t_{i+1})=y(t_i)+(t_{i+1}-t_i)y'(t_i)+\frac{(t_{i+1}-t_i)^2}{2}y''(\xi_i)$$

for some number $\xi_i\in[t_i,t_{i+1}]$. Because $h=t_{i+1}-t_i$

We have:

$$y(t_{i+1})=y(t_i)+hy'(t_i)+\frac{h^2}{2}y''(\xi_i)$$

Because $\frac{dy}{dx}=f(t,y)$

$$y(t_{i+1})=y(t_i)+hf(t_i,y(t_i))+\frac{h^2}{2}y''(\xi_i)$$

<br>

Euler's method constructs $w_i\approx y(t_i)=y_i$ for $i=1,\cdots, n$, by ***deleting the remainder term***:

Difference equation:

{:.success}

$$w_0=\alpha, w_{i+1}=w_i+hf(t_i,w_i)\quad (i=0,\cdots,n-1)$$

<img src="../../assets/images/image-20200525155405145.png" alt="image-20200525155405145" style="zoom:50%;" />



Example: Use an algorithm for Euler’s method to approximate the solution to

$$y'=y-t^2+1,0\leq t\leq 2,y(0)=0.5$$

at $t=2$. $h = 0.5$

Solution:

$f(t,y)=y-t^2+1$

$w_0=y(0)=0.5$

$w_1=w_0+hf(t,w_0)=0.5+0.5(1.5)=1.25$

$w_2=w_1+hf(t,w_1)=2.25$

$w_3=w_2+hf(t,w_2)=3.375$

and 

$y(2)\approx w_4=w_3+hf(t,w_3)=4.4375$

<br>

## Error Bounds

`Theorem`{:.error}

假设函数$f$在区间$D=\{(t,y)\vert a\leq t\leq b,\infty<y<\infty\}$上连续并且满足Lipschitz condition with constant $L$, 并且存在常数$M$, 使得对于所有$a\leq t\leq b$, 有$\vert y''(t)\vert\leq M$。令$y(t)$为IVP $y'(t)=f(t,y),a\leq t\leq b$ 的唯一解,$y(a)=\alpha$, 并且$w_0,w_1,\cdots,w_n$为Euler's method生成的近似值。那么对于$i=0,1,\cdots,n$

$$\vert y_i-w_i\vert \leq \frac{hM}{2L}[e^{L(t_i-a)}-1]$$

证明：

$$y(t_{i+1})=y(t_i)+hy'(t_i)+\frac{h^2}{2}y''(\xi_i)$$

通过Euler's method得到的

$$w_{i+1}=w_i+hf(t_i,w_i)$$

So

$$y_{i+1}-w_{i+1}=y_i-w_i+h[f(t_i,y_i)-f(t_i,w_i)]+\frac{h^2}{2}y''(\xi_i)$$

Hence

$$\vert y_{i+1}-w_{i+1}\vert=\vert y_i-w_i\vert +h\vert f(t_i,y_i)-f(t_i,w_i)\vert+\frac{h^2}{2}\vert y''(\xi_i)\vert$$

接下来我们对这个式子中的项进行处理

* 因为$f$满足Lipschitz condition

$$\vert f(t_i,y_i)-f(t_i,w_i)\vert\leq L\vert y_i-w_i\vert$$

* 因为$\vert y''(t)\vert\leq M$

$$\vert {y''(\xi_i)}\vert\leq M$$

综上：

$$\vert y_{i+1}-w_{i+1}\vert \leq (1+hL)\vert y_i-w_i\vert +\frac{h^2M}{2}$$

根据<a href="#lemma2">lemma 2</a>, 得到

$$\vert y_{i+1}-w_{i+1}\vert \leq e^{(i+1)hL}(\vert y_0-w_0\vert+\frac{h^2M}{2hL})-\frac{h^2M}{2hL}$$

因为$\vert y_0-w_0\vert=0$，$(i+1)h=t_{i+1}-t_0=t_{i+1}-a$, 所以

$$\vert y_{i+1}-w_{i+1}\vert \leq \frac{hM}{2L}(e^{(t_{i+1}-a)L}-1)$$

<br>



Note: $y''(t)$ can be computed without explicitly knowing $y(t)$

$$y''(t)=\frac{d}{dt}y'(t)=\frac{d}{dt}f(t,y(t))=\frac{\partial}{\partial t}f(t,y(t))+\frac{\partial}{\partial y}f(t,y(t))\cdot f(t,y(t))$$

<br>

The roundoff error

$w_0=\alpha+\delta_0$

$w_{i+1}=w_i+hf(t_i,w_i)+\delta_{i+1},(i=0,\cdots,n-1)$

`Theorem`{:.error}

Let $y(t)$ denote the unique solution to the  IVP 

$$y'(t)=f(t,y), a\leq t\leq b,y(a)=\alpha$$

and $w_0,w_1,\cdots,w_n$ be the approximations obtained using hte above difference equations. If $\vert\delta_i\vert<\delta$ for $i=0,1,\cdots,n$, then for each $i$

$$\vert y_i-w_i\vert\leq \frac{1}{L}(\frac{hM}{2}+\frac{\delta}{h})[e^{L(t_i-a)}-1]+\vert\delta_0\vert e^{L(t_i-a)}$$

现在error bound和h不再呈现线性关系，事实上

$$\underset{h\rightarrow 0}{lim}(\frac{hM}{2}+\frac{\delta}{h})=\infty$$

当$h=\sqrt{\frac{2\delta}{M}}$时取到最小值

但是通常$\delta$都是非常非常小的, h的这个lower bound通常不会产生影响.

<br>



# 3. Higher Order Taylor Methods

`Definition`{:.warning}



















Appendix:

<a name="lemma1">Lemma1</a>

For all $x\geq -1$ and any positive $m$, we have $0\leq (1+x)^m\leq e^{mx}$

证明：

泰勒展开

$$e^x=1+x+\frac{1}{2}x^2e^{\xi}$$

所以

$$0\leq 1+x\leq 1+x+\frac{1}{2}x^2e^{\xi}=e^x$$

因为$1+x\geq 0$，可以得到

$$0\leq (1+x)^m\leq (e^x)^m=e^{mx}$$

证毕！

<br>

<a name="lemma2">Lemma 2</a>

If $s$ and $t$ are positive real numbers, $\{a_i\}_{i=0}^k$ is a sequence satisfying $a_0\geq -\frac{t}{s}$, and 

$$a_{i+1}\leq (1+s)a_i+t$$

for each $i=0,1,\cdots,k-1$

then 

$$a_{i+1}\leq e^{(i+1)s}(a_0+\frac{t}{s})-\frac{t}{s}$$

证明

$$\begin{aligned}a_{i+1}&\leq (1+s)a_i+t\\&\leq (1+s)[(1+s)a_{i-1}+t]+t=(1+s)^2a_{i-1}+[1+(1+s)]t\\&\leq (1+s)^3a_{i-2}+[1+(1+s)+(1+s)^2]t\\&\vdots\\&\leq (1+s)^{i+1}a_0+[1+(1+s)+\cdots+(1+s)^i]t \end{aligned}$$



$1+(1+s)+\cdots+(1+s)^i=\frac{1-(1+s)^{i+1}}{1-(1+s)}=\frac{1}{s}[(1+s)^{i+1}-1]$

Thus

$$a_{i+1}\leq (1+s)^{i+1}a_0+\frac{1}{s}[(1+s)^{i+1}-1]t=(1+s)^{i+1}(a_0+\frac{t}{s})-\frac{t}{s}$$

根据前述<a href="#lemma1">lemma1</a>, 可以得到

$$a_{i+1}\leq e^{(i+1)s}(a_0+\frac{t}{s})-\frac{t}{s}$$







