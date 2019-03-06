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
<img src="http://miaochenlu.github.io/picture/20190306scanconverting.png">
* Question 1: How to draw the line?
$$from (x_1,y_1),(x_2,y_2)\LongRightarrow y=mx+b\LongRightarrow x_1+1\rightarrow y=? rounding \LongRightarrow x_1+i\rightarrow y=?rounding$$
* Question 2:How to speed up?
**Equation of a line:**  $y-m\times x+c=0$   
For a line segment joining points P(x1,y2) and
P(x2,y2)    
$ slope\quad m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}$

<img src = "http://miaochenlu.github.io/picture/20190306slope.png" width = "300">
* Digital Differential Analyzer(DDA)
$$ y_i = m·x_i+c    \\
m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}\\
x_i = x_{i_{prev}} + 1  \\
y_i = y_{i_{prev}} + m\\
illuminate\quad the\quad pixel [x_i,round(y_i)]
$$


DDA routine for rastering a line  
the line end points are(x1,y1) and (x2,y2), assumed not equal  
integer is the integer function. It's a floor function.  
Sign returns -1, 0, 1 for arguments <0, =0, >0 respectively
```c
    if abs(x2 - x1) >= abs(y2 - y1) then
        Length = abs(x2 - x1)
    else 
        Length = abs(y2 - y1)
    //select the larger of Δx or Δy to be one raster unit
    Δx = (x2 - x1) / Length
    Δy = (y2 - y1) / Length
    //round the values rather than truncate
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

--Discussion: What technique makes it faster?  
Incremental algorithm: at each step it makes incremental calculations based on the calculations done during the preceding step.
```
<table border="0"><tr>
<td><img src="http://miaochenlu.github.io/picture/20190306res2.png" width = "100" border="0" ></td>
<td><img src="http://miaochenlu.github.io/picture/20190306res1.png" width = 
"100" border="0"></td>
</tr></table>

* Bresenham Line Drawing

[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
