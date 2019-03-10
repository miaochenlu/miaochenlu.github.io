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
    Z      k1
          X  Y

        k1
    k2      Y
  Z   X
*/
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
<img src="http://miaochenlu.github.io/picture/20190307insertion.png">
<img src="http://miaochenlu.github.io/picture/20190307insertionadd.png" width = "1450">
<img src="http://miaochenlu.github.io/picture/201903073cases.png">
```c
static void RB_Insert(RBTree T, Position Z) {
    Position x = T;
    Position y = 0;
    //二叉查找树插入节点
    while( x != 0) {
        y = x;
        if(Z->Element < x->Element) 
            x = x->Left;
        else x = x->Right;
    }
    Z->Parent = y;
    
    if(y == 0) 
        T = Z;
    else if(Z->Element < y->Element) 
        y->Left = Z;
    else 
        y->Right = Z;

    Z->Left = 0;
    Z->Right = 0;
    Z->color = RED;
    RB_Insert_Fixup(T, Z);
}

static void RB_Insert_Fixup(RBTree T, Position Z) {\
    Position y;
    while(Z->Parent->color == RED) {
        if(Z->Parent == Z->Parent->Parent->Left) {
            y = Z->Parent->Parent->Right;
            //case 1
            if(y->color == RED) {
                Z->Parent->color = BLACK;
                y->color = BLACK;
                Z->Parent->Parent->color = RED;
                Z = Z->Parent->Parent;
                continue;
            }
            //case 2
            else if(Z == Z->Parent->Right) {
                Z = Z->Parent;
                Left_Rotate(T, Z);
            }
            //case 3
            Z->Parent->color = BLACK;
            Z->Parent->Parent->color = RED;
            Right_Rotate(T, Z->Parent->Parent);
        }
        else if(Z->Parent == Z->Parent->Parent->Right) {
            y = Z->Parent->Parent->Left;
            if(y->color == RED) {
                Z->Parent->color = BLACK;
                y->color = BLACK;
                Z->Parent->Parent->color = RED;
                Z = Z->Parent->Parent;
                continue;
            }
            else if(Z == Z->Parent->Left) {
                Z = Z->Parent;
                Right_Rotate(T, Z);
            }
            Z->Parent->color = BLACK;
            Z->Parent->Parent->color = RED;
            Left_Rotate(T, Z->Parent->Parent);
        }
    }
    T->color = BLACK;
}
```
reference:  
[1] CLRS

[jekyll-docs]: https://jekyllrb.com/docs/home

[jekyll-gh]: https://github.com/jekyll/jekyll

[jekyll-talk]: https://talk.jekyllrb.com/
