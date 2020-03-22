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

$\mathbb{Z}_n$: the residue riag module n

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

* It satisfies the requirements of being a riag.L

