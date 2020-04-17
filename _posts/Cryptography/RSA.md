<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417110633690.png" alt="image-20200417110633690" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200417110650856.png" alt="image-20200417110650856" style="zoom:50%;" />

RSA的底层是整数的因式分解。两个大素数相乘是很容易计算的，但是将乘积进行因式分解是非常困难的。

# 1. 加密与解密

**RSA加密:**

给定公钥$(n,e)=k_{pub}$和明文$x$, 加密函数为

$$y=e_{k_{pub}}(x)\equiv x^e\mod n$$

其中$x,y\in\mathbb{Z}_n$

<br>

**RSA解密:**

给定私钥$d=k_{pr}$以及密文$y$, 解密函数为

$$x=d_{k_{pr}}(y)\equiv y^d\mod n$$

实际应用中，$x,y,n,d$都是非常长的数字，通常为1024位或更长。

如果Bob想要将一个加密后的消息传输给Alice, 她需要拥有Alice的公钥$(n,e)$, 而Alice将用它自己的私钥$d$进行解密



# 2. 密钥生成与正确性验证

输出: 公钥$k_{pub}=(n,e)$和私钥$k_{pr}=(d)$

(1). 选择两个大素数$p,q$

(2). 计算$n=p\cdot q$

(3). 计算$\varphi(n)=(p-1)(q-1)$, $\varphi(n)$是欧拉函数

(4). 选择满足以下条件的$e\in\{1,2,\cdots,\varphi(n)-1\}$

$$gcd(e,\varphi(n))=1$$

(5). 计算满足以下条件的私钥$d$

$$d\cdot e\equiv 1\mod \varphi(n)$$

<br>

* 为什么要满足$gcd(e,\varphi(n))=1$呢？

Recall lec1: 

`Theorem`{:.error}

For positive integers $a,n>1$, let $d=gcd(a,n)$最大公约数. Then there exist integers $u,v$, s.t. $ua+vn=d$. 裴蜀定理

`Proposition`{:.warning}

For $1\leq a\leq n-1$, $\bar{a}$ has an inverse in $\mathbb{Z}^n$ iff $gcd(a,n)=1$

因此，条件$gcd(e,\varphi(n))=1$保证了$e$模$\varphi(n)$的逆元存在, 这样，私钥$d$始终存在。可以使用扩展欧几里得算法计算

<img src="/Users/jones/Downloads/IMG_72683E7E7D9E-1.jpeg" alt="IMG_72683E7E7D9E-1" style="zoom:40%;" />

### 正确性验证

RSA的核心为$d_{k_{pr}}(y)=d_{k_{pr}}(e_{k_{pub}(x)})\equiv(x^e)^d\equiv x^{de}\equiv x\mod n$

我们来证明$d_{k_{pr}}(e_{k_{pub}(x)})= x$

proof:

首先根据公钥私钥的构建规则$d\cdot e\equiv 1\mod \varphi(n)$, 这等价于

$$d\cdot e\equiv 1+t\cdot \varphi(n)$$

其中t是整数

那么，$d_{k_{pr}}(y)\equiv x^{de}\equiv x^{1+t\cdot\varphi(n)}\equiv x\cdot x^{t\cdot\varphi(n)}\equiv x\cdot(x^{\varphi(n)})^t\mod n$

这意味着我们要证明$x\equiv (x^{\varphi(n)})^t\cdot x\mod n$

根据欧拉定理，如果$gcd(y,n)=1\Rightarrow 1\equiv y^{\varphi(n)}\mod n$, 得到一个推广

$$1\equiv 1^t\equiv (x^{\varphi(n)})^t\mod n$$

那我们就从两种情况来证明$x\equiv (x^{\varphi(n)})^t\cdot x\mod n$

***case (1)***. $gcd(x,n)=1$

此时欧拉定理成立

$$d_{k_{pr}}(y)=(x^{\varphi(n)})^t\cdot x\equiv 1^t\cdot x\equiv x\mod n$$

***case (2)***. $gcd(x,n)=gcd(x,p\cdot q)\not=1$

因为p,q均为素数，所以x必须拥有下面因子的任何一个

$$x=r\cdot p \quad x=s\cdot q$$

其中r,s均为整数，并且$r<q,s<p$

不失一般性地假设: $x=r\cdot p$

可以得到$gcd(x,q)=1$, 那么如下欧拉定理满足

$$1\equiv 1^t\equiv (x^{\varphi(q)})^t \mod q$$

因此$d_{k_{pr}}(y)=(x^{\varphi(n)})^t\cdot x\equiv(x^{\varphi(p)\cdot\varphi(q)})^t\cdot x\equiv   (x^{\varphi(p)})^{(t\cdot(q-1))}\cdot x\equiv 1\cdot x\equiv x\mod n$

<br>

# 4. 加密与解密：快速指数运算





