# Pivoting

$$\left[\begin{matrix}0.03 & 59.14\\5.291&-6.130\end{matrix}\right]\left[\begin{matrix}x_1\\x_2\end{matrix}\right]=\left[\begin{matrix}59.17\\46.78\end{matrix}\right]$$

The exact solution is $x_1=10, x_2=1$

Using 4-digit arithmetic Gauss Elimination

$$m_{21}=\frac{5.291}{0.003}$$

$x_2\approx 1.001, x_1\approx\frac{59.17-59.14\times 1.001}{0.003}=-10$

误差：$m_{21}$有很大的误差，$x_2=1.001$不准确，回带的时候又被极度放大

<br>

### A. Partial pivoting:

$$m_{21}=\frac{a_{21}^{(1)}}{a_{11}^{(1)}}=\frac{5.291}{0.003}$$

This problem arises when the pivot element $a_{kk}^{(k)}$ is small relative to the entries $a_{ij}^{k}$ , for $k\leq i\leq n$ and $k\leq j\leq n$.

Switch row p, k for large pivoting element: 比如第一行和第二行进行交换，那么第一行第一列的pivot会编程第二行第一列的pivot。在这里我们从第k行往下看这列最大的元素所在行为p, 将第k行与第p行进行交换，那么我们可以得到相对较大的pivot的系数

$$\vert a_{pk}^{(k)}\vert=\underset{k\leq i\leq n}{max}\vert a_{ik}^{(k)}\vert$$

<br>

Is that enough?

$$\left[\begin{matrix}30 & 591400\\5.291&-6.130\end{matrix}\right]\left[\begin{matrix}x_1\\x_2\end{matrix}\right]=\left[\begin{matrix}591700\\46.78\end{matrix}\right]$$

After partial pivoting

$$m_{21}=9.1764$$

$x_2\approx 1.001, x_1\approx\frac{591700-591400\times 1.001}{30}=-10$

虽然这里没有除以很小的数，但是1.001乘了很大的数，因此我们真正要考虑的应该是$\frac{591400}{30}$这一项的大小



### B. Scaled Partial Pivoting(scaled-column pivoting): Largest relative to the entries in itw row.

&emsp;Step1: Define a scale factor $s_i$ for each row as $$s_i=\underset{1\leq j\leq n}{max}\vert a_{ij}\vert$$,  $s_i$是第i行最大的元素

&emsp; Step2: Determine the smallest $p\geq k$ such that $$\frac{\vert a_{pk}^{(k)}\vert}{s_p}=\underset{k\leq i\leq n}{max}\frac{\vert a_{ik}^{(k)}\vert}{s_i}$$  and interchange the pth and the kth rows. 对行做遍历，计算这一行pivot的系数和$s_i$的比值，来观察相对大小

> Note:
>
> &emsp;The scaled factors $s_i$ must be computed only once, otherwise this method would be too slow.

<br>

Example

Solve the linear system using three-digit rounding arithmetic.

$$\begin{aligned}2.11x_1-4.21x_2+0.921x_3&=2.01\\ 4.01x_1+10.2x_2-1.12x_3&=-3.09\\1.09x_1+0.987x_2+0.823x_3&=4.21\end{aligned}$$

We have $s_1=4.21,s_2=10.2,s_3=1.09$

So, $\frac{\vert a_{11}\vert}{s_1}=\frac{2.11}{4.21}=0.501$, $\frac{\vert a_{21}\vert}{s_1}=\frac{4.01}{10.2}=0.393$, $\frac{\vert a_{31}\vert}{s_3}=\frac{1.09}{1.09}=1$

因此交换第三行和第一行,得到

$$\left[\begin{matrix}1.09&0.987&0.832&4.21\\4.01&10.2&-1.12&-3.09\\2.11&-4.21&0.921&2.01\end{matrix}\right]$$

消元后得到

$$\left[\begin{matrix}1.09&0.987&0.832&4.21\\0&6.57&-4.18&-18.6\\0&-6.12&-0.689&-6.16\end{matrix}\right]$$

$$\frac{\vert a_{22}\vert}{s_2}=\frac{6.57}{10.2}=0.644$$ and $\frac{vert a_{32}\vert}{s_3}=\frac{6.12}{4.21}=1.45$, 注意这里 $s_2,s_3$并没有重新计算

因此交换第二行和第三行

$$\left[\begin{matrix}1.09&0.987&0.832&4.21\\0&-6.12&-0.689&-6.16\\0&6.57&-4.18&-18.6\end{matrix}\right]$$

消元后得到

$$\left[\begin{matrix}1.09&0.987&0.832&4.21\\0&-6.12&-0.689&-6.16\\0&0&-4.92&-25.2\end{matrix}\right]$$

最后解得

$$\left[\begin{matrix}x_1\\x_2\\x_3\end{matrix}\right]=\left[\begin{matrix}-0.436\\0.430\\5.12\end{matrix}\right]$$

最理想的是进行行列交换，行交换会减小$m_{21}$的误差，列交换会减小$x_1$的误差

<br>

### C. Complete Pivoting(or maximal pivoting)

Search all the entries $a_{ij}$ for $i,j=k,\cdots,n$, to find the entry with the largest magnitude. Both row and column interchanges are performed to bring this entry to the pivot position.

遍历整个矩阵去寻找最大的数，然后进行行列交换，计算量相当大

trick: 0不能做分母，无穷大的误差放大。碰到很小的数也是同样的道理，解丧失了区分度





### D. Amount of Computation

* Partial Pivoting: Requires about $O(n^2)$ additional comparisons.
* Scaled Partial Pivoting: Requires about $O(n^2)$ additional comparisons and $O(n^2)$ divisions.
* Complete Pivoting: Requires about $O(\frac{n^3}{3})$ additional comparisons.





# Matrix Factorization

Matrix Form of Gaussian Elimination

&emsp;Step1: $m_{i1}=\frac{a_{i1}}{a_{11}}\quad (a_{11}\not=0)$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200322212140210.png" alt="image-20200322212140210" style="zoom:50%;" />

所有L都是下三角矩阵。一个很好的性质是下三角矩阵在乘法操作下依然是下三角矩阵。它的inverse依然是下三角矩阵

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200322212529061.png" alt="image-20200322212529061" style="zoom:50%;" />

<a href="https://blog.csdn.net/wo94chunjie/article/details/103859745">LU分解有什么意义1</a>

<a href="https://www.zhihu.com/question/52451868?sort=created">LU分解有什么意义2</a>



<br>

Theorem: If Gaussian elimination can be performed on the linear system $A\vec{x}=\vec{b}$ **withour row interchanges**, then the matrix A can be factored into the product of a lower-triangular matrix L and an upper-triangular matrix U.

If L has to be unitary(对角线全1)， then the factorization is unique

Proof(for uniqueness):

If the factorization is NOT unique, then there exist $L_1,U_1,L_2,U_2$ such that 

$$A=L_1U_1=L_2U_2$$

$$U_1U_2^{-1}=L_1^{-1}L_2U_2U_2^{-1}=L_1^{-1}L_2$$

$U_1U_2^{-1}$是上三角矩阵, $L_1^{-1}L_2$是下三角矩阵且对角线元素都是1. 因此

$$U_1U_2^{-1}=L_1^{-1}L_2U_2U_2^{-1}=L_1^{-1}L_2=I$$

<br>

# Special Types of Matrices

### A.Strictly Diagonally Dominant Matrix

对角线元素比同一行其他元素加起来还大

$$\vert a_{ii}\vert>\underset{j=1,j\not=i}{\overset{n}{\sum}} \vert a_{ij}\vert\quad for\;each\; i=1,\cdots,n$$

Theorem: A strictly diagonally dominant matrix is <u>nonsingular</u>. Morever, Gaussian elimination can be performed <u>without row or column interchanges</u>, and the computations will be <u>stable</u> with respect to the growth of roundoff errors.

> 注意：
>
> 并不是所有nonsigular矩阵不需要进行行列交换来进行高斯消元。
>
> 考虑
>
> $$\left[\begin{matrix} 1 & 0 & 0\\ 0&1&0\\0&0&1\end{matrix}\right]$$
>
> $$A=\left[\begin{matrix} 0 & 1 & 0\\ 1&0&0\\0&0&1\end{matrix}\right]$$, 这是一个非奇异矩阵，因为A等于I乘上一个基本矩阵。但是在高斯消元的时候需要进行行交换。
>
> 在高斯消元时进行行列交换，我们可以看到，是为了提高精度。但是反pivoting会增大误差



### 3. Choleski's Method for Positive Definite Matrix

symmetric positive definite: SPD对称正定矩阵

Definition: A matrix A is <u>positive definite</u> if it is <u>symmetric</u> and if $\vec{x}^tA\vec{x}>0$ for every n-dimensional vector $\vec{x}\not=0$

<br>

Review: A is positive definite

* $A^{-1}$ is positive definite as well, and $a_{ii}>0$
* $max\vert a_{ij}\vert\leq max\vert a_{kk}\vert$; $(a_{ij})^2<a_{ii}a_{jj}\;for\;each\;i\not=j$

* Each of A's leading principal submatrices $A_k$(顺序主子式) has a positive determinant.

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200322215946660.png" alt="image-20200322215946660" style="zoom:50%;" />

Example:

Determine the Cholesky $LL^t$ factorization of the positive deﬁnite matrix

$$A=\left[\begin{matrix}4&-1&1\\-1&4.25&2.75\\1&2.75&3.5\end{matrix}\right]$$

Solution:

$$A=\left[\begin{matrix}a_{11}&a_{21}&a_{31}\\a_{21}&a_{22}&a_{23}\\a_{31}&a_{32}&a_{33}\end{matrix}\right]=\left[\begin{matrix}l_{11}&0&0\\l_{21}&l_{22}&0\\l_{31}&l_{32}&l_{33}\end{matrix}\right]\left[\begin{matrix}l_{11}&l_{21}&l_{31}\\0&l_{22}&l_{32}\\0&0&l_{33}\end{matrix}\right]$$

$=\left[\begin{matrix}l_{11}^2 &l_{11}l_{21}&l_{11}l_{31}\\ l_{11}l_{21}&l_{21}^2+l_{22}^2&l_{21}l_{31}+l_{22}l_{32}\\l_{11}l_{31}&l_{21}l_{31}+l_{22}l_{32}&l_{31}^2+l_{32}^2+l_{33}^2\end{matrix}\right]$

Thus

$a_{11}: 4=l_{11}^2\Rightarrow l_{11}=2\quad a_{21}: -1=l_{11}l_{21}\Rightarrow l_{21}=-0.5 $

$a_{31}: 1=l_{11}l_{31}\Rightarrow l_{31}=0.5\quad a_{22}: 4.25=l_{21}^2+l_{22}^2\Rightarrow l_{22}=2 $

$a_{32}: 2.75=l_{21}l_{31}+l_{22}l_{32}\Rightarrow l_{32}=1.5\quad a_{33}: 3.5=l_{31}^2+l_{32}^2+l_{33}^2\Rightarrow l_{33}=1 $

$$A=LL^t=\left[\begin{matrix}2&0&0\\-0.5&2&0\\0.5&1.5&1\end{matrix}\right]\left[\begin{matrix}2&-0.5&0.5\\0&2&1.5\\0&0&1\end{matrix}\right]$$

### C. Crout Reduction for Tridiagonal Linear System

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20200322220222715.png" alt="image-20200322220222715" style="zoom:50%;" />

Example