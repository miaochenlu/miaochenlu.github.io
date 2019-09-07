---

layout: post

title: "Computer Graphics"

date: 2019-03-04 12:21:05 +0800

categories: jekyll update

---



<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      equationNumbers: {
        autoNumber: "AMS"
      }
    },
    tex2jax: {
      inlineMath: [ ['$','$'], ['\(', '\)'] ],
      displayMath: [ ['$$','$$'] ],
      processEscapes: true,
    }
  });
</script>
<script type="text/javascript"
        src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>



<h1><a name="top">目录</a></h1>
<a href="#rotation">旋转与四元数</a>

<a href="#scan_convert_line">光栅扫描图形学</a>

<h1><a name="rotation">旋转与四元数</a></h1>
## 1 三维空间中的旋转



<video src="http://miaochenlu.github.io/video/Jietu20190907-093543.mp4" width=400></video>


如图，我们要将D点绕旋转轴Axis旋转到A点

假设这个旋转轴u经过原点, $u=(x,y,z)^T$,我们要将BD(向量v)绕着轴旋转 $\alpha$变换到 BA(向量v')

\\

* 首先我们要得到 $\mathbf{u}$的单位向量 $\hat{\mathbf{u}}$

为什么？ 

因为 $u=(x,y,z)^T$有三个自由度，确定了旋转轴的方向与长度

但是我们不需要用到长度的信息

因此，我们可以将u转换成单位向量 $\hat{\mathbf{u}}$，这样我们可以减少一个自由度

其中 $\hat{\mathbf{u}}=\frac{\mathbf{u}}{||\mathbf{u||}}$



* 分解旋转

可以将 $\mathbf{v}$绕轴 $\mathbf{u}$的旋转分解到平行 $\mathbf{u}$和 垂直$\mathbf{u}$两个方向,

旋转前$v=v_{\parallel}+v_{\perp}$

旋转后 $v'=v_{\parallel}'+v_{\perp}'$

接下来我们通过u、v来求解 $v_{\parallel}$和 $v_{\perp}$的表达式

### 先讨论 $v_{\parallel}$





<img src="http://miaochenlu.github.io/picture/image-20190907100316520.png" width=300>

$v_{\parallel}$的方向就是旋转轴u的方向，大小就是v在u上投影的大小，根据向量点乘的几何意义，我们可以得到投影公式，如下

$$\begin{aligned}
v_{\parallel}&=proj_u(v)\\
&=\frac{u\cdot v}{||u||}\hat{u}\\
&=(\hat{u}\cdot v)\hat{u}
\end{aligned}$$





因为 $v_{\parallel}$平行于旋转轴，所以他是不变的，即旋转之后 $v_{\parallel}'=v_{\parallel}$

\\

### **再讨论 $v_{\perp}$**

在讨论 $v_{\parallel}$时我们得出 $v_{\parallel}=(\hat{u}\cdot v)\hat{u}$

所以我们可以求得 $v_{\perp}=v-v_{\parallel}=v-(\hat{u}\cdot v)\hat{u}$



左图是旋转过程示意图，右图是俯视图

<figure class="half">
  <img src="http://miaochenlu.github.io/picture/image-20190907112926608.png" width=300>
  <img src="http://miaochenlu.github.io/picture/image-20190907120930786.png" width=350>
</figure>

接下来我们在俯视图的2D视角来推导

由于一个向量 $v_{\perp}$不足以表示旋转，我们构造一个垂直于 $v_{\perp}$和旋转轴u的向量 $\mathbf{w}$, $\mathbf{w}$可以通过叉乘获得
$$
\mathbf{w}=\mathbf{u}\times \mathbf{v_{\perp}}
$$
将 $v_{\perp}'$分别向 $\mathbf{w}$和 $v_{\perp}$投影，得到$v_w'$和$v_v'$

接下来我们来推导 $v_{\perp}'$
$$
\begin{aligned}
v_{\perp}'&=v_v'+v_w'\\
&=v_{\perp}cos\alpha +wsin\alpha\\
&=v_{\perp}cos\alpha+(\mathbf{u}\times \mathbf{v_{\perp}})sin\alpha
\end{aligned}
$$
带入 $v_{\perp}=v-v_{\parallel}$到 $\mathbf{u}\times\mathbf{v_{\perp}}$
$$
\begin{aligned}
\mathbf{u}\times \mathbf{v_{\perp}}&=\mathbf{u}\times(v-v_{\parallel})\\
&=\mathbf{u}\times v-\mathbf{u}\times v_{\parallel}\\
&=\mathbf{u}\times\mathbf{v}\qquad\qquad(\mathbf{u}||v_{\parallel})
\end{aligned}
$$
所以最终我们得到
$$
v_{\perp}'=v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha
$$
\\

### 最终旋转公式

以上两步，我们得到
$$
v_{\parallel}'=v_{\parallel}\\
v_{\perp}'=v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha
$$
结合这两个结果
$$
\begin{aligned}
v'&=v_{\parallel}'+v_{\perp}'\\
&=v_{\parallel}+v_{\perp}cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha
\end{aligned}
$$
带入 $v_{\parallel}=(\hat{u}\cdot v)\hat{u}$和 $v_{\perp}=v-(\hat{u}\cdot v)\hat{u}$

最终得到
$$
\begin{aligned}
v'&=(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}}+(\mathbf{v}-(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}})cos\alpha+(\mathbf{u}\times\mathbf{v})sin\alpha\\
&=\mathbf{v}cos\alpha+(1-cos\alpha)(\hat{\mathbf{u}}\cdot \mathbf{v})\hat{\mathbf{u}}+(\mathbf{u}\times\mathbf{v})sin\alpha
\end{aligned}
$$

## 2 四元数



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
$$
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
