---

layout: post

title: "Red-black Tree"

date: 2019-03-07 12:21:05 +0800

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



<img src="http://miaochenlu.github.io/picture/20190307inductionrbtree.png">
<img src="http://miaochenlu.github.io/picture/20190307heightrbtree.png">
<img src="http://miaochenlu.github.io/picture/20190307rotation.png">

```c
/*
        k2
    k1      Z
   X  Y

        k1
    X       k2
           Y   Z
*/
static Position SingleRotateWithLeft(Position K2) {
    Position K1;
    K1 = K2->Left;
    K2->Left = K1->Right;
    K1->Right = K2;
    K2->Height = max(Height(K2->Left), Height(K2->Right)) + 1;
    K1->Height = max(Height(K1->Left), K2->Height) + 1;
    return K1;
}
/*
        k2
    Z      k1
          X  Y

        k1
    k2      Y
  Z   X
*/
static Position SingleRotateWithRight(Position K2) {
    Position K1;
    K1 = K2->Right;
    K2->Right = K1->Left;
    K1->Left = K2;
    K2->Height = max(Height(K2->Left), Height(K2->Right)) + 1;
    K1->Height = max(Height(K1->Right), K2->Height) + 1;
    return K1;
}
```
[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
