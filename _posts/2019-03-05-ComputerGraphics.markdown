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
inlineMath: [['$','$']]
}
});
</script>
<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>


# 光栅扫描图形学
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
$$ y_i = m·x_i+c    \\
m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}\\
x_i = x_{i_{prev}} + 1  \\
y_i = y_{i_{prev}} + m\\
illuminate\quad the\quad pixel [x_i,round(y_i)]
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
    //select the larger of Δx or Δy to be one raster unit
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
$$P_i=2x_idy+2dy+(2b-1)dx-2y_idx$$  
$$If\quad P_i>0,then \quad y_{i+1}=y_i+1,else \quad y_{i+1}=y_i$$    
$$P_{i+1}=2x_{i+1}dy-2y_{i+1}dx+2dy+(2b-1)dx,\quad note\quad that\quad x_{i+1}=x_i+1$$   
$$P_{i+1}=p_i+2dy-2(y_{i+1}-y_i)dx$$  

另一种思路
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
[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
