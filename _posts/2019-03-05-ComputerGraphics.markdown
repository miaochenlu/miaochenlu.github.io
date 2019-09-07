---

layout: post

title: "Computer Graphics"

date: 2019-03-04 12:21:05 +0800

categories: jekyll update

---



<script type="text/x-mathjax-config">
MathJax.Hub.Config({
tex2jax: {
skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      inlineMath: [ ['$','$'], ['\(', '\)'] ],
      displayMath: [ ['$$','$$'] ],
      processEscapes: true,
}
});
</script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
tex2jax: {
skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      inlineMath: [ ['$','$'], ['\(', '\)'] ],
      displayMath: [ ['$$','$$'] ],
      processEscapes: true,
}
});
</script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>
<h1><a name="top">目录</a></h1>
<a href="#rotation">旋转与四元数</a>

<a href="#scan_convert_line">光栅扫描图形学</a>







<br/>

<br/>

<br/>

<br/>

<h1><a name="rotation">旋转与四元数</a></h1>
## 1 三维空间中的旋转  

<video id="video" controls="" preload="none" width="400">
      <source id="mp4" src="http://miaochenlu.github.io/video/Jietu20190907-093543.mp4" type="video/mp4" >
      <p>Your user agent does not support the HTML5 Video element.</p>
    </video>

如图，我们要将D点绕旋转轴Axis旋转到A点

假设这个旋转轴u经过原点, $u=(x,y,z)^T$,我们要将BD(向量v)绕着轴旋转 $\alpha$变换到 BA(向量v')


* 首先我们要得到 $\mathbf{u}$的单位向量 $\hat{\mathbf{u}}$

为什么？   

> 因为 $u=(x,y,z)^T$有三个自由度，确定了旋转轴的方向与长度  
> 但是我们不需要用到长度的信息.   
>  因此，我们可以将u转换成单位向量 $\hat{\mathbf{u}}$，这样我们可以减少一个自由度.   
> 其中$\hat{\mathbf{u}}=\frac{\mathbf{u}}{\mathbf{||u||}}$                         

<br/>

* 分解旋转

可以将 $\mathbf{v}$绕轴 $\mathbf{u}$的旋转分解到平行 $\mathbf{u}$和 垂直$\mathbf{u}$两个方向,

旋转前$v=v_{\parallel}+v_{\perp}$

旋转后 $v'=v_{\parallel}'+v_{\perp}'$

接下来我们通过u、v来求解 $v_{\parallel}$和 $v_{\perp}$的表达式

### 1.1  $v_{\parallel}$ 推导 

<img src="http://miaochenlu.github.io/picture/image-20190907100316520.png" width="300" />

$v_{\parallel}$的方向就是旋转轴u的方向，大小就是v在u上投影的大小，根据向量点乘的几何意义，我们可以得到投影公式，如下  

$$\begin{aligned}
v_{\parallel}&=proj_u(v)\\
&=\frac{u\cdot v}{||u||}\hat{u}\\
&=(\hat{u}\cdot v)\hat{u}
\end{aligned}$$

因为 $v_{\parallel}$平行于旋转轴，所以他是不变的，即旋转之后 $v_{\parallel}'=v_{\parallel}$ 

<br/>


### 1.2 $v_{\perp}$推导
在讨论 $v_{\parallel}$时我们得出 $v_{\parallel}=(\hat{u}\cdot v)\hat{u}$

所以我们可以求得 $v_{\perp}=v-v_{\parallel}=v-(\hat{u}\cdot v)\hat{u}$

左图是旋转过程示意图，右图是俯视图    

<figure>
  <img src="http://miaochenlu.github.io/picture/image-20190907112926608-7829821.png" width="300" />
  <img src="http://miaochenlu.github.io/picture/image-20190907120930786-7829821.png" width="300" />
</figure>

接下来我们在俯视图的2D视角来推导

由于一个向量 $v_{\perp}$不足以表示旋转，我们构造一个垂直于 $v_{\perp}$和旋转轴u的向量 $\mathbf{w}$, $\mathbf{w}$可以通过叉乘获得

$$\mathbf{w}=\mathbf{u}\times \mathbf{v_{\perp}}$$

将 $v_{\perp}'$分别向 $\mathbf{w}$和 $v_{\perp}$投影，得到$v_w'$和$v_v'$

接下来我们来推导 $v_{\perp}'$

$$\begin{aligned}
v_{\perp}'&=v_v'+v_w'\\
&=v_{\perp}cos\alpha +wsin\alpha\\
&=v_{\perp}cos\alpha+(\mathbf{u}\times \mathbf{v_{\perp}})sin\alpha
\end{aligned}$$

代入 $v_{\perp}=v-v_{\parallel}$到 $\mathbf{u}\times\mathbf{v_{\perp}}$

$$\begin{aligned}
\mathbf{u}\times \mathbf{v_{\perp}}&=\mathbf{u}\times(v-v_{\parallel})\\
&=\mathbf{u}\times v-\mathbf{u}\times v_{\parallel}\\
&=\mathbf{u}\times\mathbf{v}\qquad\qquad(\mathbf{u}||v_{\parallel})
\end{aligned}$$

所以最终我们得到

$$v_{\perp}'=v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha$$

<br/>

### 1.3 最终旋转公式

以上两步，我们得到

$$v_{\parallel}'=v_{\parallel}\\
v_{\perp}'=v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha$$

结合这两个结果  

$$\begin{aligned}
v'&=v_{\parallel}'+v_{\perp}'\\
&=v_{\parallel}+v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha
\end{aligned}$$

<br/>

代入 $v_{\parallel}=(\hat{u}\cdot v)\hat{u}$和 $v_{\perp}=v-(\hat{u}\cdot v)\hat{u}$

最终得到

$$\begin{aligned}
v'&=(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}}+(\mathbf{v}-(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}})cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha\\
&=\mathbf{v}cos\alpha+(1-cos\alpha)(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}}+(\mathbf{u}\times\mathbf{v})sin\alpha
\end{aligned}$$

## 2 四元数

### 2.1 定义

* **复数**由实数加上虚数单位 $i$组成，其中$i^2=-1$

* **四元数**由实数加上三个元素 $i、j、k$组成

每个四元数都是 $1、i、j、k$这些单位元的线性组合，四元数q可以表示成 

$$q=a+bi+cj+dk\quad(a,b,c,d\in\mathbf{R})$$

其中： $i^2=j^2=k^2=ijk=-1$

通常我们也会将四元数写成标量和向量的有序对，标量是实部，三维向量表示虚部

$$q=[s,\mathbf{v}]\quad(v=\left[\begin{matrix}x\\y\\z\end{matrix}\right], s,x,y,z\in\mathbf{R})$$

### 2.2 运算与性质

#### 2.2.1 范数

复数的范数对应他在复平面向量的长度

四元数的范数的定义和高维向量一样，虽然很难想象在四维空间中的长度



如果我们用 $q=a+bi+cj+dk$表示四元数，那么他的范数定义为

$$||q||=\sqrt{a^2+b^2+c^2+d^2}$$

如果我们用标量向量有序对 $q=[s,\mathbf{v}]$的形式表示四元数，那么他的范数定义为

$$||q||=\sqrt{s^2+\mathbf{||v||}^2}$$



#### 2.2.2 四元数加减法

四元数加减法就是将对应分量相加减

假设我们有两个四元数 $q_1=a+bi+cj+dk$, $q_2=e+fi+gj+hk$

$$q_1+q_2=(a+e)+(b+f)i+(c+g)j+(d+h)k$$

$$q_1-q_2=(a-e)+(b-f)i+(c-g)j+(d-h)k$$

或者写成下面的形式

$$q_1\pm q_2=[s\pm t, \mathbf{v}\pm \mathbf{u}]$$



#### 2.2.3 标量乘法

假设我们有一个标量s,和一个四元数 $q=a+bi+cj+dk$

$sq=sa+sbi+scj+sdk$

标量乘法满足交换律，即 $sq=qs$



#### 2.2.4 四元数乘法
##### 2.2.4.1 乘法推导

在进入四元数乘法之前，我们先来看一下四元数单位元的乘法

根据$i^2=j^2=k^2=ijk=-1$

$$\begin{aligned}ijk=&-1\\iijk=&-i\\-jk=&-i\\jk=&i\end{aligned}$$

同理

$$\begin{aligned}ijk=&-1\\ ijkk=&-k\\-ij=&-k\\ij=&k\end{aligned}$$

利用 $ij=k$，我们可以推导出

$$\begin{aligned}ij=&k\\ijj=&kj\\-i=&kj\\kj=&-i\end{aligned}$$



我们可以得到下面这张乘数表

| $\times$ | 1    | $i$  | $j$  | $k$  |
| -------- | ---- | ---- | ---- | ---- |
| 1        | 1    | $i$  | $j$  | $k$  |
| $i$      | $i$  | -1   | $k$  | $-j$ |
| $j$      | $j$  | $k$  | -1   | $i$  |
| $k$      | $k$  | $j$  | -$i$ | -1   |

我们可以看到四元数的乘法是不满足交换律的。（结合律，分配律满足）



接下来我们进入四元数的乘法

假设我们有两个四元数 $q_1=a+bi+cj+dk$和 $q_2=e+fi+gj+hk$
$$
\begin{aligned}
q_1q_2&=(a+bi+cj+dk)(e+fi+gj+hk)\\
&=ae+afi+agj+ahk+\\
&\quad \,bei+bfi^2+bgij+bhik+\\
&\quad \,cej+cfji+cgj^2+chjk+\\
&\quad \,dek+dfki+dgkj+dhk^2\\
&=ae+afi+agj+ahk+\\
&\quad \,bei-bf+bgk-bhj+\qquad (i^2=-1,ij=k,ik=-j)\\
&\quad \,cej-cfk-cg+chi+\qquad(ji=-k,j^2=-1,jk=i)\\
&\quad \,dek+dfj-dgi-dh\qquad\quad (ki=j,kj=-i,k^2=-1)\\
&=(ae-(bf+cg+dh))+\\
&\quad \,(af+be+ch-dg)i+\\
&\quad \,(ag-bh+ce+df)j+\\
&\quad \,(ah+bg-cf+de)k
\end{aligned}
$$

#### 2.2.4.2 矩阵形式
从上述结果，我们可以得到乘法的矩阵形式  

$$q_1q_2=\left[\begin{matrix}a&-b&-c&-d\\
b&a&-d&c\\
c&d&a&-b\\
d&-c&b&a
\end{matrix}\right]
\left[\begin{matrix}e\\f\\g\\h\end{matrix}\right]$$

这相当于左乘$q_1$所做的矩阵变换

#### 2.2.4.3 Grassmann积  

2.2.4.1中我们得到乘积的表达式为  

$$\begin{aligned}q_1q_2
&=(ae-(bf+cg+dh))+\\
&\quad \,(be+af+ch-dg)i+\\
&\quad \,(ce+ag-bh+df)j+\\
&\quad \,(de+ah+bg-cf)k
\end{aligned}$$  

<br/>
现在我们可能还不能发现什么  
<br/>

假设令
$$\mathbf{v}=\left[\begin{matrix}b\\c \\ d \end{matrix}\right],\mathbf{u}=\left[\begin{matrix}f\\g\\h\end{matrix}\right]$$

点乘：$\mathbf{v}\cdot\mathbf{u}=bf+cg+dh$  

定义叉乘（想不到吧，叉乘就是在这里被定义的）  

$$\begin{aligned}\mathbf{v}\times\mathbf{u}&=\left|\begin{matrix}
\mathbf{i}&\mathbf{j}&\mathbf{k}\\
b&c&d\\
f&g&h
\end{matrix}\right|\\
&=(ch-dg)\mathbf{i}-(bh-df)\mathbf{j}+(bg-cf)\mathbf{k}
\end{aligned}$$
<br/>
<br/>

$q_1q_2$的标量部分可以用$ae-\mathbf{v}\cdot\mathbf{u}$表示  
向量部分可以用$a\mathbf{u}+e\mathbf{v}+\mathbf{v}\times\mathbf{u}$表示   
因此，用标量向量有序对表示，我们可以得到  
<br/>
对于任意四元素$q_1=[s,\mathbf{v}]$,$q_2=[t,\mathbf{u}]$  
$$q_1q_2=[st-\mathbf{v}\cdot\mathbf{u},s\mathbf{u}+t\mathbf{v}+\mathbf{v}\times\mathbf{u}]$$

#### 2.2.5 纯四元数
仅有虚部的四元数数是纯四元数
$$v=[0,\mathbf{v}]$$
$v$就是一个纯四元数

两个纯属元数的相乘会发生什么呢？
假设有两个纯四元数$v=[0,\mathbf{v}]$,$u=[0,\mathbf{u}]$
$$vu=[0-\mathbf{v}\cdot\mathbf{u},\mathbf{v}\times\mathbf{u}$$
之后会用到这条性质

#### 2.2.6 逆及共轭
定义四元数$q$的逆$q^{-1}$  

$$qq^{-1}=q^{-1}q=1(q\neq 0)$$  
计算逆非常困难，下面我们会发现通过共轭来计算逆非常方便  

$q=[s,\mathbf{v}]$的共轭为$q^*=[s,-\mathbf{v}]$  

二者相乘  

$$\begin{aligned}
qq^*&=[s,\mathbf{v}][s,-\mathbf{v}]\\
&=[s^2-\mathbf{v}\cdot(-\mathbf{v}),s(-\mathbf{v}+s\mathbf{v}+\mathbf{v}\times\mathbf{-v})]\\
&=[s^2+||v||^2,0]\\
&=||q||^2
\end{aligned}
$$   

易证 $qq^*=q^*q$，满足交换律  

<br/>
接下来我们求逆  

$$\begin{aligned}
qq^{-1}&=1\\
q^*qq^{-1}&=q^*\\
(q^*q)q^{-1}&=q^*\\
||q||^2\cdot q^{-1}&=q^*\\
q^{-1}&=\frac{q^*}{||q||^2}
\end{aligned}$$


<p id="back-to-top"><a href="#top">返回目录</a></p>
<h1><a name="scan_convert_line">光栅扫描图形学</a></h1>
## Scan converting lines
<img src="http://miaochenlu.github.io/picture/20190306scanconverting.png" width = "300">

* Question 1: How to draw the line?
$$from (x_1,y_1),(x_2,y_2) \\ 
\Rightarrow y=mx+b \\
\Rightarrow x_1+1 \rightarrow y=? rounding \\ 
\Rightarrow x_1+i \rightarrow y=?rounding$$
* Question 2:How to speed up? 
\\
\\
\\
**Equation of a line:**  $y-m\times x+c=0$   
For a line segment joining points P(x1,y2) and
P(x2,y2)    
$ slope\quad m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}$
<img src = "http://miaochenlu.github.io/picture/20190306slope.png" width = "300">

### **Digital Differential Analyzer(DDA)**
$$ y_i = m\cdot x_i+c    \\
m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}\\
x_i = x_{i_{prev}} + 1  \\
y_i = y_{i_{prev}} + m\\
illuminate\quad the\quad pixel [x_i,round(y_i)]$$

```
DDA routine for rastering a line  
the line end points are(x1,y1) and (x2,y2), assumed not equal  
integer is the integer function. It's a floor function.  
Sign returns -1, 0, 1 for arguments <0, =0, >0 respectively
    if abs(x2 - x1) >= abs(y2 - y1) then
        Length = abs(x2 - x1)
    else 
        Length = abs(y2 - y1)
    select the larger of Δx or Δy to be one raster unit
    Δx = (x2 - x1) / Length
    Δy = (y2 - y1) / Length
    round the values rather than truncate
    x = x1 + 0.5
    y = y1 + 0.5
    begin main loop
    i = 1
    while(i <= Length)
        setpixel(Integer(x), Integer(y))
        x = x + Δx
        y = y + Δy
        i = i + 1
    endwhile
finish
```
--Discussion: What technique makes it faster?  
Incremental algorithm: at each step it makes incremental calculations based on the calculations done during the preceding step.  
But it uses floating point operations.

-results  
<table border="0"><tr>
<td><img src="http://miaochenlu.github.io/picture/20190306res2.png" width = "100" border="0" ></td>
<td><img src="http://miaochenlu.github.io/picture/20190306res1.png" width = 
"100" border="0"></td>
</tr></table>

### **Bresenham Line Drawing**
<img src="http://miaochenlu.github.io/picture/20190306Bresenham.png" width = "300">

* Analysis:  
先考虑第一八分圆域，$$0\leq \Delta y \leq \Delta x$$
事实上，$y_{i+1}$的选择只有两种情况：$y_i \quad or\quad y_i+1$
$$ y = m(x_i+1)+b\\
d_1=y-y_i\\
d_2=y_i+1-y\\
If\quad d_1-d_2>0 \quad y_{i+1} = y_i, else\quad y_{i+1}=y_i
$$
$$d_1-d_2=2y-2y_i-1=2m(x_i+1)+2b-2y_i-1\\
=2\frac{dy}{dx}·x_i+2\frac{dy}{dx}+2b-2y_i-1$$  
$On\quad each\quad side\quad of\quad the\quad equation \quad multiply\quad dx,denote \quad d_1-d_2 \quad as\quad P_i$  
$$P_i=2x_idy+2dy+(2b-1)dx-2y_idx\\
If\quad P_i>0,then \quad y_{i+1}=y_i+1,else \quad y_{i+1}=y_i\\  
P_{i+1}=2x_{i+1}dy-2y_{i+1}dx+2dy+(2b-1)dx,\quad note\quad that\quad x_{i+1}=x_i+1\\ 
P_{i+1}=p_i+2dy-2(y_{i+1}-y_i)dx$$  

use e  
```
    initialize variables
    x = x1
    y = y1
    Δx = x2 - x1
    Δy = y2 - y1
    m = Δy / Δx
    initialize e to compensate for a nonzero intercept
    e = m - 1 / 2
    begin the main loop
    for i = 1 to Δx
        setpixel(x, y)
        while(e > 0)
            y = y + 1
            e = e - 1
        end while
        x = x + 1
        e = e + m
    next i
finish
```
上述会用到浮点运算和除法，采用整数运算比较好  
$$\overline e = 2e\Delta x$$
```
    initialize variables
    x = x1
    y = y1
    Δx = x2 - x1
    Δy = y2 - y1
    initialize e to compensate for a nonzero intercept
    e = 2*Δy-Δx
    begin the main loop
    for i = 1 to Δx
        setpixel(x, y)
        while(e > 0)
            y = y + 1
            e = e-2*Δx
        end while
        x = x + 1
        e = e + 2*Δy
    next i
finish
```

reference:
[1] 计算机图形学的算法基础

<p id="back-to-top"><a href="#top">返回目录</a></p>


[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/