# ODE

## A. Euler & high order Euler's method

考虑n阶泰勒展开

$y_{i+1}=y_i+hf(t_i,y_i)+\frac{h^2}{2}f'(t_i,y_i)+\cdots+\frac{h^n}{n!}f^{(n-1)}(t_i,y_i)+\frac{h^{n+1}}{(n+1)!}f^{(n)}(\xi_i,y(\xi_i))$

$$\begin{aligned}w_0&=\alpha\\ w_{i+1}&=w_i+hT^{(n)}(t_i,w_i)\end{aligned}$$

其中$i=0,1,\cdots,n-1$

$$T^{(n)}(t_i,w_i)=f(t_i,w_i)+\frac{h}{2}f'(t_i,w_i)+\cdots+\frac{h^{n-1}}{n!}f^{(n-1)}(t_i,w_i)$$

也可以写成

$w_{i+1}=w_i+hf(t_i,w_i)+\frac{h^2}{2}f'(t_i,w_i)+\cdots+\frac{h^n}{n!}f^{(n-1)}(t_i,w_i)$

那么local truncation error:

$\tau_{i+1}(h)=\frac{y_{i+1}-(y_i+hT^{(n)}(t_i,y_i))}{h}=\frac{y_{i+1}-y_i}{h}-T^{(n)}(t_i,y_i)=\frac{h^{n}}{(n+)!}f^{(n)}(\xi_i,y(\xi_i))$

$=\frac{remainder}{h}$

## B. 由euler's method引发的血案

#### 隐式欧拉法

$$\begin{aligned}w_0&=\alpha\\w_{i+1}&=w_i+hf(t_{i+1},w_{i+1}) \end{aligned}$$

写成

$$\begin{aligned}w_0&=\alpha\\w_{i+1}&=w_i+hf(t_{i+1},w_i+hf(t_i,w_i)) \end{aligned}$$



#### Trapezoidal Method / modified Euler's method

平均斜率

$$\begin{aligned}w_0&=\alpha\\w_{i+1}&=w_i+\frac{h}{2}[f(t_i,w_i)+f(t_{i+1},w_{i+1})] \end{aligned}$$

local truncation error $O(h^2)$. 

$$\begin{aligned}w_0&=\alpha\\w_{i+1}&=w_i+\frac{h}{2}[f(t_i,w_i)+f(t_{i+1},w_i+hf(t_i,w_i))] \end{aligned}$$

#### Double-Step Method

$$\begin{aligned}w_0&=\alpha\\w_{i+1}&=w_{i-1}+2hf(t_i,w_i) \end{aligned}$$

local truncation error $O(h^4)$. 

## C. Runge-Kutta Method

#### 二阶推导

$$\begin{aligned} w_{i+1}&=w_i+h[\lambda_1 K_1+\lambda_2 K_2]\\ K_1&=f(t_i,w_i)\\ K_2&=f(t_i+ph,w_i+phK_1) \end{aligned}$$



对$y$求二阶导

$$\begin{aligned} y''(t)&=\frac{d}{dt}f(t,y)\\&=f_t(t,y)+f_y(t,y)\frac{dy}{dt}\\ &=f_t(t,y)+f_y(t,y)f(t,y) \end{aligned}$$

Step1: Write the Taylor expansion of $K_2$ at $(t_i,y_i)$

$$\begin{aligned} K_2&=f(t_i+ph,y_i+phK_1)\\ &=f(t_i,y_i)+phf_t(t_i,y_i)+phf_y(t_i,y_i)f(t_i,y_i)+O(h^2)\\&=y'(t_i)+phy''(t_i)+O(h^2) \end{aligned}$$

Step2: 将 $K_2$ 代入第一个方x程

$$\begin{aligned}w_{i+1}&=y_i+h\{\lambda_1y't(_i)+\lambda_2[y'(t_i)+phy''(t_i)+O(h^2)] \}\\&=y_i+(\lambda_1+\lambda_2)hy'(t_i)+\lambda_2ph^2y''(t_i)+O(h^3) \end{aligned}$$

Step3: Find $\lambda_1,\lambda_2$ and $p$ 使得$\tau_{i+1}=\frac{y_{i+1}-w_{i+1}}{h}=O(h^2)$

我们要使得该式子符合泰勒展开，则

$$\begin{aligned}w_{i+1}&=y_i+(\lambda_1+\lambda_2)hy'(t_i)+\lambda_2ph^2y''(t_i)+O(h^3)\\y_{i+1}&=y_i+hy'(t_i)+\frac{h^2}{2}y''(t_i)+O(h^3) \end{aligned}$$

因此

$$\lambda_1+\lambda_2=1,\lambda_2p=\frac{1}{2}$$

3个未知数，两个方程，有无数解



#### 4阶

$$\begin{aligned}w_{i+1}&=y_i+\frac{h}{6}[K_1+2K_2+2K_3+K_4]\\K_1&=f(t_i,w_i)\\K_2&=f(t_i+\frac{h}{2},w_i+\frac{h}{2}K_1) \\ K_3&=f(t_i+\frac{h}{2},w_i+\frac{h}{2}K_2)\\ K_4&=f(t_i+h,w_i+hK_3) \end{aligned}$$



#### 高阶

$$\begin{aligned}w_{i+1}&=y_i+h[\lambda_1K_1+\lambda_2K_2+\cdots+\lambda_mK_m]\\K_1&=f(t_i,w_i)\\K_2&=f(t_i+\alpha_2h,w_i+\beta_{21}hK_1)\\&\vdots\\K_m&=f(t_i+\alpha_mh,w_{i} +\beta_{m1} hK_1+\beta_{m2}hK_2+\cdots+\beta_{m\;m-1}hK_{m-1}) \end{aligned}$$



## D. Multistep Methods

$$y'=f(t,y), a\leq t\leq b, y(a)=\alpha$$

在区间$[t_i,t_{i+1}]$上积分

$$y(t_{i+1})-y(t_i)=\int_{t_i}^{t_{i+1}}y'(t)dt=\int_{t_i}^{t_{i+1}}f(t,y(t))dt$$

因此

$$y(t_{i+1})=y(t_i)+\int_{t_i}^{t_{i+1}}f(t,y(t))dt$$

但是，因为我们不知道$y(t)$的表达式，无法做积分。因此我们想找$f(t,y(t))$的一个插值多项式$P(t)$来做积分

有一些data points $(t_0,w_0),(t_1,w_1),\cdots,(t_i,w_i)$。假定$y(t_i)\approx w_i$, 那么

$$y(t_{i+1})\approx w_i+\int_{t_i}^{t_{i+1}}P(t)dt$$



#### Adams-Bashforth explicit m-step technique

Use the Newton backward-difference formula to interpolate on $(t_i,f_i), (t_{i-1},f_{i-1}),\cdots,(t_{i+1-m},f_{i+1-m})$ and obtain $P_{m-1}(t)$. Or let $t=t_i+sh$, then $s\in[0,1]$ and we have

$$\int_{t_i}^{t_{i+1}}f(t,y(t))dt=h\int_0^1P_{m-1}(t_i+sh)ds+h\int_0^1R_{m-1}(t_i+sh)ds$$

$\Rightarrow$ Explicit formula

$$w_{i+1}=w_i+h\int_0^1 P_{m-1}(t+sh)ds$$



## E. Higher-Order Equations

Example: 使用modifies Euler's method to solve the following IVP using $h=0.1$

$y''-2y'+y=te^t-1.5t+1,0\leq t\leq 0.2$

$y(0)=0,y'(0)=-0.5$

Solution:

Modified Euler's method为

$$\begin{aligned} w_{i+1}&=w_i+h[\frac{1}{2}K_1+\frac{1}{2}K_2]\\ K_1&=f(t_i,w_i)\\ K_2&=f(t_i+h,w_i+hK_1) \end{aligned}$$



令$u_1=y(t),u_2=y'(t)$

可以写出方程组

$$\begin{aligned}u_1'&=y'=u_2\\ u_2'&=y''=te^t-1.5t+1-u_1(t)+2u_2(t) \end{aligned}$$

初值$u_1(0)=0, u_2(0)=-0.5$

因此

* round 1

$$\vec{K_1}(0)=\vec{f}(0,\vec{u}(0))=\left[\begin{matrix}-0.5\\0\end{matrix}\right]$$

$$\vec{K_2}(0)=\vec{f}(0.1,\left[\begin{matrix}-0.5\\0\end{matrix}\right])=\left[\begin{matrix}-0.5\\0.0105\end{matrix}\right]$$

$$\vec{u}(0.1)=\vec{u}(0)+0.05(\vec{K_1}(0)+\vec{K_2}(0))=\left[\begin{matrix}-0.05\\-0.4995\end{matrix}\right]$$

* round 2

$$\vec{K_1}(0.1)=\vec{f}(0.1,\vec{u}(0.1))=\left[\begin{matrix}-0.4995\\0.0115\end{matrix}\right]$$

$$\vec{K_2}(0.1)=\vec{f}(0.2,\left[\begin{matrix}-0.1000\\-0.4984\end{matrix}\right])=\left[\begin{matrix}-0.4984\\0.0475\end{matrix}\right]$$

$$\vec{u}(0.2)=\vec{u}(0.1)+0.05(\vec{K_1}(0.1)+\vec{K_2}(0.1))=\left[\begin{matrix}-0.0999\\-0.4966\end{matrix}\right]$$







