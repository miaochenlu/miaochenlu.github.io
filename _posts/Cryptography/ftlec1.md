A cryptosystem is a five-tuple $(\mathscr{P},\mathscr{C},\mathscr{K},\mathscr{E},\mathscr{D})$

1. $\mathscr{P}$ is a finite set of possible <u>plaintexts</u>;
2. $\mathscr{C}$ is a finite set of possible <u>ciphertexts</u>
3. $\mathscr{K}$, the keyspace, is a finite set of possible keys
4. For each $K\in \mathscr{K}$, there is an encryption rule $e_K\in\mathscr{E}$ and a corresponding decryption rule $d_K\in\mathscr{D}$. Each $e_K:\mathscr{P}\rightarrow\mathscr{C}$ and $d_K:\mathscr{C}\rightarrow \mathscr{P}$ are functions such that 

$$d_K(e_K(x))=x$$

for every plaintext element $x\in\mathscr{P}$. 要check的是这个条件

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200322160214426.png" alt="image-20200322160214426" style="zoom:50%;" />

热知识，量子卫星是用来分发密钥的，实现实时密钥共享

<br>

$$\mathbf{X}=x_1x_2\cdots x_n$$

block cipher, 每个$x_i$被视为一个block

for some integer $n\geq 1$, where each plaintext symbol $x_i\in\mathscr{P}, 1\leq i\leq n$. Each $x_i$ is encrypted using the encryption rule $e_K$ specified by the predetermined key K. Hence, Alice computes $y_i=e_K(x_i), 1\leq i\leq n$, and the resulting ciphertext string 

$$\mathbf{y}=y_1y_2\cdots y_n$$

is sent over the channel.

When Bob receives $y_1y_2\cdots y_n$, he decrypts it using the decryptio function $d_K$, obtaining the original plaintext string $x_1x_2\cdots x_n$.

<br>

一些数论知识

$\mathbb{Z}_n$: the residue riag module n,  $n=\{\bar{0},\bar{1},\cdots,\overline{n-1}\}$

* elements: $\bar{i} = \{i+nk:k\in\mathbb{Z}\}, 0\leq i\leq n-1$

&emsp;&emsp;$\overline{ i+n} =\bar{i}\quad \overline{ i+kn} =\bar{i}$

* addition is a map function: $\mathbb{Z}_n\times \mathbb{Z}_n\mapsto\mathbb{Z}_n$

&emsp;&emsp;$(\bar{i},\bar{j})\mapsto\overline{i+j}$

and we write $\bar{i}+\bar{j}:=\overline{i+j}$

* multiplication is a map function: $\mathbb{Z}_n\times \mathbb{Z}_n\mapsto\mathbb{Z}_n$

&emsp;&emsp;$(\bar{i},\bar{j})\mapsto\overline{ij}$

It is well-defined,  $(\overline{i+kn}, \overline{j+ln})\mapsto\overline{ij}$

for any $k,l\in\mathbb{Z}$, this is equiv to $\overline{(i+kn)(j+ln)}=\overline{ij}$

i.e. $(i+kn)(j+ln)-ij$ is a multiple of n

* It satisfies the requirements of being a riag.

e.g. 

$\bar{i}\cdot(\bar{j}+\bar{k})=\bar{i}\cdot\overline{j+k}=\overline{i(j+k)}$

$\bar{i}\cdot\bar{j}+\bar{i}\cdot\bar{k}=\overline{ij}+\overline{ik}=\overline{ij+ik}$

We write $a\equiv b\pmod n \;if\; \bar{a}=\bar{b}\;in\;\mathbb{Z}^n,i.e.\; n\vert a-b$

An element of $\mathbb{Z}^n$ has an inverse if there exists $\bar{b}\in\mathbb{Z}^n$, s.t. $\bar{a}\cdot\bar{b}=\bar{1}$, and we write $\bar{a}^{-1}=\bar{b}$

The additive inverse of $\bar{a}$ is an element $\bar{b}$, s.t. $\bar{a}+\bar{b}=\bar{0}$, add we denote $-\bar{a}=\bar{b}$

We write $\bar{x}-\bar{y}$ for $\bar{x}+(-\bar{y})$

It is clear that $-\bar{a}+\overline{-a}=\bar{0}$, so $-\bar{a}=\overline{-a}$

<br>

Recall: For positive integers $a,n>1$, let $d=gcd(a,n)$最大公约数. Then there exist integers $u,v$, s.t. $ua+vn=d$. 裴蜀定理

Proof:Define $S=\{ua+vn: u,v\in\mathbb{Z}\}$, Let $d'$ be the smallest positive integer in S, we have $d\vert d'$, so $d\leq d'$

We now show that $d'$ divides both a and n

Suppose $a=qd'+r,0\leq r\leq d'-1$

Now $r=a-qd'\in S$. By the min of d, we obtain $r=0$, i.e. $d'|a$

Similarly, $d'|n$, So $d'|gcd(a,n)=d$

<br>

We can use the extended Euclidean algorithm to determine the pair $(u,v)$

e.g. $n=26,a=7$

$26=3\times 7+5\Rightarrow 5=n-3a$

$7=1\times 5+2\Rightarrow 2=a-1\times(n-3a)=-n+4a$

$5=2\times 2+1$

$1=5-a\times 2=(n-3a)-2\times(-n+4a)=3n-11a$

Hence $u=3,v=-11,un+va=1$

<br>

Proposition:

For $1\leq a\leq n-1$, $\bar{a}$ has an inverse in $\mathbb{Z}^n$ iff $gcd(a,n)=1$

Proof: 

* ###### If $gcd(a,n)=1$, then $\exist integers\;u,v$  s.t. $un+va=1$. Taking modulo n. We obtain $\bar{v}\cdot \bar{a}=\bar{1}$ in $\mathbb{Z}^n$. So $\bar{v}$ is the inverse of $\bar{a}$ in $\mathbb{Zn}$

* If $\bar{v}\cdot\bar{a}=\bar{1}$ for some $\bar{v}\in\mathbb{Z}^n$. then $va-1$ is a multiple of $n$. Say $va-1\equiv un$ for some $u\in\mathbb{Z}$. Then $gcd(a,n)$ divides $va+un=1\Rightarrow gcd(a,n)=1$



<br>

$\mathbb{Z}^*_n=\{\bar{a}:1\leq a\leq n-1, gcd(a,n)=1\}$

=the set of elements of $\mathbb{Z}^n$ of have a multiplication inverse.

$\vert \mathbb{Z}^*_n\vert=\varphi(n)$ Euler totient function

FACT:

If n is a prime p, then $\varphi(p)=p-1$

If $n=n_1n_2$, $gcd(n_1,n_2)=1$, then $\varphi(n)=\varphi(n_1)\varphi(n_2)$

If $n=p^r$, then $\varphi(p^r)=(p-1)p^{(r-1)}$

<br>

Kinkhoff principle(in cryptoanalysis):

the opponent knows 

### Shift cipher

Let $\mathscr{P}=\mathscr{C}=\mathscr{K}=\mathbb{Z}_{26}$. For $0\leq K\leq 25$, define

$$e_K(x)=(x+K)\mod 26$$

$$d_K(y)=(y-K)\mod 26$$

$(x,y\in\mathbb{Z}_{26})$

proof:

$d_K(e_K(x))=d_K(\bar{x}+\bar{k})=\bar{x}+\bar{k}-\bar{k}=\bar{x}$

Notation: $a\mod n =$ the remainder of a modulo n

Cryptoanalysis of shift cipher: brute force, exhaustive search

e.g Caeser cipher, k=3

### Affine cipher

$\mathscr{P}=\mathscr{C}=\mathbb{Z}_{26}$,  Let $\mathscr{K}=\{(a,b)\in\mathbb{Z}_{26}\times \mathbb{Z}_{26}:gcd(a,26)=1\}$, 

For $K=(a,b)\in \mathscr{K}$, define

$$e_K(x)=ax+b\mod 26$$

$$d_K(x)=a^{-1}(y-b)\mod 26$$

Here $a^{-1}$ stands for the elements of $\mathbb{Z}_{26}$, s.t. $ax\equiv 1\mod 26$

注意: $a^{-1}$是a的乘法逆元，如 $7^{-1}\mod 26=15$

### Substitution cipher

$\mathscr{P}=\mathscr{C}=\mathbb{Z}_{26}$. $\mathscr{K}$ consists of all possible permutations of the 26 symbols $0,1,\cdots, 25$. For each permutation $\pi\in\mathscr{K}$, define

$$e_{\pi}(x)=\pi(x)$$

and define

$$d_{\pi}(y)=\pi^{-1}(y)$$

where $\pi^{-1}$ is the inverse permutation to $\pi$