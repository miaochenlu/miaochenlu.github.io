# Block Ciphers

比如我们有一串明文，一个block为512bits, 先把一个block加密后传输出去，再加密下一个block

先要介绍一种cipher: ***iterated cipher***

The cipher requires the specification of a round function and a key schedule, and the encryption of plaintext will proceed through $Nr$ similiar rounds.

<br>

Alice Bob share a secret key $k$ (say 512bits)

The encryption $e_k(x)$ is realized by an iterated cipher

<img src="/Users/jones/Downloads/37F2FFCEBEF04F7048BCD1693992EB58.png" alt="37F2FFCEBEF04F7048BCD1693992EB58" style="zoom:92%;" />

每一个单元硬件上都是一样的

Here, the $K^{(i)}$'s are the round keys generated from the key k by a public algorithm

第i轮：

$\omega^{i+1}=g(w^i,K^{i+1})$

其中$w^{i+1}$是next state, $w^i$是current state, $K^{i+1}$是round key

the output $y=e_k(x)$ is taken to be $w^{Nr}$

The decryption 



<br>

# 2. Substitution-Permutation Network(SPN)

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416235258781.png" alt="image-20200416235258781" style="zoom:50%;" />

在介绍这个cryptosystem之前，先来了解一点背景知识。

假设$l,m$都是正整数，明文和密文都是二进制向量，他们的长度是$lm$。

SPN的两个重要组件： $\pi_S,\pi_P$

* $\pi_S:\{0,1\}^l\mapsto \{0,1\}$

这是一个替换函数：将一组二进制数映射为另一组二进制数

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416234651713.png" alt="image-20200416234651713" style="zoom:50%;" />

* $\pi_P:\{1,\cdots,lm\}\mapsto\{1,\cdots,lm\}$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200416234723017.png" alt="image-20200416234723017" style="zoom:50%;" />

<br>

Let $l,m$ and $Nr$ be positive integers, let $\pi_S:\{0,1\}^l\mapsto \{0,1\}$ be a permutation, and let $\pi_P:\{1,\cdots,lm\}\mapsto\{1,\cdots,lm\}$ be a permutation. Let $P=C=\{0,1\}^{lm}$, and let $K\subseteq(\{0,1\}^{lm})^{Nr+1}$ consist of all possible key schedules that could be derived from an initial key $K$ using the key scheduling algorithm.  For a key schedule $(K^1,\cdots, K^{Nr+1})$, we encrypt the plaintext $x$ using below algorithm.

<img src="/Users/jones/Downloads/IMG_0C837C5A8EB4-1.jpeg" alt="IMG_0C837C5A8EB4-1" style="zoom:40%;" />

* whitening(round key mixing):

 $u^i=w_i\oplus K^{i+1}$ (addition modulo 2)

* substitution: 也称为S-box

$\pi_S:\{0,1\}^l\mapsto\{0,1\}^l$ 也就是一个替换

<img src="/Users/jones/Downloads/IMG_D1611D6FDD7F-1.jpeg" alt="IMG_D1611D6FDD7F-1" style="zoom:90%;" />

* permutation:

$\pi_P:\{1,\cdots,lm\}\mapsto\{1,\cdots,lm\}$ 是对原序列的重新排列

$w^{i+1}=\pi_p(v^{i+1})=(v^{i+1}_{\pi_p(1)},\cdots,v^{i+1}_{\pi_p(lm)})$

<br>

Remark: (1) whitening,  permutation是线性变换

(2) Eash Si is called a S-box (S=substitution)

We assume that the Si's are all equal. It is a function $\pi_S:\mathbb{Z}_2^l\mapsto\mathbb{Z}_2^l$, bijection

To store this function, we need $2^l\cdot l$  bits of memory. For an input $a\in\mathbb{Z}_2^l$, we can determine $\pi_S(a)$ by ***looking up the table***.

Sbox是唯一一个非线性组件

(3) Permutation is realized by ***rearranging the wires*** in the hardware.

<img src="/Users/jones/Downloads/IMG_531D6110C50D-1.jpeg" alt="IMG_531D6110C50D-1" style="zoom:30%;" />

(4) In conclusion, the round function is highly efficient. 因为没有复杂计算, Sbox只需要查表，Permutation不涉及计算

<br>

最后一轮slightly different

<img src="/Users/jones/Downloads/IMG_872EE16043F8-1.jpeg" alt="IMG_872EE16043F8-1" style="zoom:30%;" />

# 3. Linear cryptanalysis

* 令 $X=(x_1,\cdots,x_{lm})$对应为为plaintext随机变量， $X_i\in\{0,1\}$, 假设$X_i$相互独立

$$Pr[X_{i_1}=c_1,X_{i_2}=c_2,\cdots,X_{i_r}=c_r]=Pr[X_{i_1}=c_1]\cdot Pr[X_{i_2}=c_2]\cdots Pr[X_{i_r}=c_r]$$

$a\in \mathbb{Z}_2^{lm}$, $a\cdot X=a_1X_1+\cdots+a_{ml}X_{ml}$, 这是一个定义在$\{0,1\}$上的随机变量

* 我们用$U^{Nr}$来表示随机变量$u^{Nr}$, 他的取值为$\mathbb{Z}_2^{lm}$, 他的概率分布是根据$X,K$得到的

* 对于一个定义在$\{0,1\}$上的随机变量$Z$。Pr$[Z=0]=\frac{1}{2}$

linear cryptanalysis的策略是

1. 通过理论分析得到$a\cdot X+b\cdot U^{Nr}$的线性组合，it has a large bias(in absolute eevalue)
2. 