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
## 直线生成算法
**Equation of a line** $y-m\times x+c=0$   
For a line segment joining points P(x1,y2) and
P(x2,y2)    
$ slope\quad m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}$

<img src = "http://miaochenlu.github.io/picture/20190306slope.png">
* Digital Differential Analyzer(DDA)
$$ y_i = m·x_i+c    \\
m=\frac{y_2-y_1}{x_2-x_1}=\frac{dy}{dx}\\
x_i = x_{i_{prev}} + 1  \\
y_i = y_{i_{prev}} + m\\
illuminate the pixel [x_i,round(y_i)]
$$
[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
