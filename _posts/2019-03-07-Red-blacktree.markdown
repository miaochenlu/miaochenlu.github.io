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
static void Left_Rotate(RBTree T, Position K2) {
    //设置K2的右儿子为K1
    Position K1 = K2->Right;
    //将K2的右儿子设置为K1的左儿子
    K2->Right = K1->Left;
    //如果K1的右儿子非空，则将他的父亲设置为K2
    if(K1->Left != 0) 
        K1->Left->Parent = K2;
    //将K1的父亲设置为K2的父亲
    K1->Parent = K2->Parent;
    
    if(K2->Parent == 0)
        T = K2; //如果K2的父亲是空节点，将K1设置为根节点
    else if (K2 == K2->Parent->Left)
        K2->Parent->Left = K1;//如果K2是他父亲的左儿子，则将K1设置为左儿子
    else 
        K2->Parent->Right = K1;//如果K2是他父亲的右儿子，则将K2设置为右儿子
    K1->Left = K2;  //将K2设置为左儿子
    K2->Parent = K1;//K2的父亲设置为K1
}

/*
        k2
    k1      Z
   X  Y

        k1
    X       k2
           Y   Z
*/
static void Right_Rotate(RBTree T, Position K2) {
    Position K1 = K2->Left;
    K2->Left = K1->Right;
    if(K1->Right != 0) 
        K1->Right->Parent = K2;
    K1->Parent = K2->Parent;
    if(K2->Parent == 0) 
        T = K1;
    else if(K2 == K2->Parent->Left)
        K2->Parent->Left = K1;
    else 
        K2->Parent->Right = K1;
    K1->Right = K2;
    K2->Parent = K1;
}
```
[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
