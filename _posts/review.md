# SIFT

## 1.

## 2. 为什么使用梯度信息而不直接使用像素值

## 3. 各种不变性的解释

**1.  平移不变：**SIFT是局部特征，只提取关键点点附近矩形区域的sample，所以该物体移动到任何地方提取的feature都是类似的。同时因为是划grid去提取，即便关键点稍微偏移一下feature也基本没有变化，有点类似于HOG或者CNN的pooling。

**2.  旋转不变：**在计算grid里面的梯度bin前需要旋转到主方向，因此有了一定的旋转不变性。

**3. 光照不变**： 计算feature vector的时候进行了归一化、卡阈值之后又一次归一化，抵消了部分光照的影响。

**4. 尺度不变：**通过前一步算LoG得到的尺度来确定计算feature的范围，所以特征对应了一个尺度。所以原本不同尺度的图片能转换到相似的尺度提取相似的特征



## 3. SIFT步骤

SIFT[scale-invariant feature transform]特征是图像的局部特征，其对旋转、尺度缩放、亮度变化保持不变性，对视角变化、仿射变换、噪声也保持一定程度的稳定性；



SIFT算法可以分解为四步

1. 尺度空间极值检测：搜索所有尺度上的图像位置。通过高斯微分函数来识别潜在的对于尺度和旋转不变的兴趣点。

2. 关键点定位：在每个候选的位置上，通过一个拟合精细的模型来确定位置和尺度。关键点的选择依据于它们的稳定程度。

3. 方向确定：基于图像局部的梯度方向，分配给每个关键点位置一个或多个方向。所有后面的对图像数据的操作都相对于关键点的方向、尺度和位置进行变换，从而提供对于这些变换的不变性。

4. 关键点描述：在每个关键点周围的邻域内，在选定的尺度上测量图像局部的梯度。这些梯度被变换成一种表示，这种表示允许比较大的局部形状的变形和光照变化




### A. 尺度空间极值检测

$$G(x,y)=\frac{1}{2\pi \sigma ^2}e^{-\frac{(x-m/2)^2+(y-n/2)^2}{2\sigma ^2}}$$

尺度空间理论的基本思想是：在图像信息处理模型中引入一个被视为尺度的参数，通过连续变化尺度参数获得多尺度下的尺度空间表示序列，对这些序列进行尺度空间主轮廓的提取，并以该主轮廓作为一种特征向量，实现边缘、角点检测和不同分辨率上的特征提取等。

尺度空间可以用高斯金字塔表示，Tony Lindeberg指出尺度规范化的LoG(Laplacion of Gaussian)算子具有真正的尺度不变性，Lowe使用高斯差分金字塔近似LoG算子，在尺度空间检测稳定的关键点。

尺度空间方法将传统的单尺度图像信息处理技术纳入尺度不断变化的动态分析框架中，更容易获取图像的本质特征。尺度空间中各尺度图像的模糊程度逐渐变大，能够模拟人在距离目标由近到远时目标在视网膜上的形成过程。



#### 尺度空间的表示

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219104245912.png" alt="image-20191219104245912" style="zoom:40%;" />

一个图像的尺度空间，$L(x,y,\sigma)$定义为一个变化尺度[变化的是$\sigma$]的高斯函数$G(x,y,\sigma)$与原图像$I(x,y)$的卷积

$$L(x,y,\sigma)=G(x,y,\sigma)*I(x,y)$$

$$G(x,y)=\frac{1}{2\pi \sigma ^2}e^{-\frac{(x-m/2)^2+(y-n/2)^2}{2\sigma ^2}}$$

<br/>

***构建高斯金字塔***

* 对图像做不同尺度的高斯模糊[调整$\sigma$]
* 对图像做降采样



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219105034161.png" alt="image-20191219105034161" style="zoom:40%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191223164353053.png" alt="image-20191223164353053" style="zoom:50%;" />

图像的金字塔模型是指，将原始图像不断降阶采样，得到一系列大小不一的图像，由大到小，从下到上构成的塔状模型。原图像为金子塔的第一层，每次降采样所得到的新图像为金字塔的一层(每层一张图像)，每个金字塔共n层。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219105337581.png" alt="image-20191219105337581" style="zoom:50%;" />

为了让尺度体现其连续性，高斯金字塔在简单降采样的基础上加上了高斯滤波。

将图像金字塔每层的一张图像使用不同参数[$\sigma$]做高斯模糊，使得金字塔的每层含有多张高斯模糊图像，将金字塔每层多张图像合称为一组(Octave)，金字塔每层只有一组图像，每组含有多张(也叫层Interval)图像。另外，降采样时，高斯金字塔上一组图像的初始图像(底层图像)是由前一组图像的倒数第三张图像隔点采样得到的。


***构建高斯差分金字塔***

2002年Mikolajczyk在详细的实验比较中发现尺度归一化的高斯拉普拉斯函数$\sigma ^2\nabla^2G $ 的极大值和极小值能够产生最稳定的图像特征，这和其它的特征提取函数，例如：梯度，Hessian或Harris角特征类似。

而Lindeberg早在1994年就发现***高斯差分函数***（Difference of Gaussian ，简称DOG算子）与尺度归一化的高斯拉普拉斯函数非常近似。

$$\frac{\partial G}{\partial \sigma}=\frac{-2\sigma^2+x^2+y^2}{2\pi \sigma^5}e^{-(x^2+y^2)/{2\sigma^2}}$$

$$\nabla^2G=\frac{\partial^2 G}{\partial x^2}+\frac{\partial^2 G}{\partial y^2}=\frac{-2\sigma^2+x^2+y^2}{2\pi \sigma^6}e^{-(x^2+y^2)/{2\sigma^2}}$$

$$\Rightarrow \frac{\partial G}{\partial \sigma}=\sigma \nabla^2G$$

$$\sigma\nabla^2G=\frac{\partial G}{\partial \sigma}\approx \frac{G(x,y,k\sigma)-G(x,y,\sigma)}{k\sigma-\sigma}$$

因此$$G(x,y,k\sigma)-G(x,y,\sigma)\approx (k-1)\sigma ^2\nabla ^2 G$$

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219110027492.png" alt="image-20191219110027492" style="zoom:50%;" />



$$\begin{aligned}D(x,y,\sigma)=&(G(x,y,k\sigma)-G(x,y,\sigma))*I(x,y)\\=&L(x,y,k\sigma)-L(x,y,\sigma)\end{aligned}$$

为了在每组中检测S个尺度的极值点，则DOG金字塔每组需S+2层图像，而DOG金字塔由高斯金字塔相邻两层相减得到，则高斯金字塔每组需S+3层图像

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191223164421850.png" alt="image-20191223164421850" style="zoom:50%;" />

***空间极值点检测***

关键点是由DOG空间的局部极值点组成的，关键点的初步探查是通过同一组内各DoG相邻两层图像之间比较完成的。为了寻找DoG函数的极值点，每一个像素点要和它所有的相邻点比较，看其是否比它的图像域和尺度域的相邻点大或者小。中间的检测点和它同尺度的8个相邻点和上下相邻尺度对应的9×2个点共26个点比较，以确保在尺度空间和二维图像空间都检测到极值点。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219112644432.png" alt="image-20191219112644432" style="zoom:40%;" />

### B. 关键点定位

* Discard points with DOG value ***below threshold*** (low contrast)
* However, points along edges may have high contrast in one direction but low in another 

* Compute principal curvatures from eigenvalues of 2x2 Hessian matrix, and ***limit ratio*** (Harris approach): 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219113121266.png" alt="image-20191219113121266" style="zoom:50%;" />

### C. 方向确定

- Take 16x16 square window around detected feature 
- Compute edge orientation (angle of the gradient - 90) for each pixel 
- Throw out weak edges (threshold gradient magnitude) 
- Create histogram of surviving edge orientations 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219113757045.png" alt="image-20191219113757045" style="zoom:50%;" />

方向直方图的峰值则代表了该特征点处邻域梯度的方向，以直方图中最大值作为该关键点的***<u>主方向</u>***。

为了增强匹配的鲁棒性，只保留峰值大于主方向峰值80％的方向作为该关键点的辅方向。因此，对于同一梯度值的多个峰值的关键点位置，在相同位置和尺度将会有多个关键点被创建但方向不同。仅有15％的关键点被赋予多个方向，但可以明显的提高关键点匹配的稳定性。

### D. 关键点特征描述

通过以上步骤，对于每一个关键点，拥有三个信息：

***<u>（位置，尺度，方向）</u>***。

接下来就是为每个关键点建立一个***描述符***，用一组向量将这个关键点描述出来，使其不随各种变化而改变，比如光照变化、视角变化等等。这个描述子不但包括关键点，也包含关键点周围对其有贡献的像素点，并且描述符应该有较高的独特性，以便于提高特征点正确匹配的概率。

SIFI 描述子h(x, y,θ)是对特征点附近邻域内高斯图像梯度统计结果的一种表示，它是一个三维的阵列，但通常将它表示成一个矢量。矢量是通过对三维阵列按一定规律进行排列得到的。特征描述子与特征点所在的尺度有关，因此，对梯度的求取应在特征点对应的高斯图像上进行。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191223164625763.png" alt="image-20191223164625763" style="zoom: 33%;" />

为了保证特征矢量具有***旋转不变性***，需要以特征点为中心，将特征点附近邻域内图像梯度的位置和方向旋转一个方向角θ，即将原图像x轴转到与主方向相同的方向。

旋转公式

$$\left[\begin{matrix}x'\\y'\end{matrix} \right]=\left[\begin{matrix}cos\theta & -sin\theta\\ sin\theta & cos\theta\end{matrix}\right]$$

$$W=\left[\begin{matrix}0&w_{12}&w_{13}\\  w_{21}&0&w_{23}\\ w_{31}&w_{32}&0\end{matrix}\right]$$

然后

- Divide the 16x16 window into a 4x4 grid of cells (2x2 case shown below) 
- Compute an orientation histogram for each cell 
- 16 cells * 8 orientations = 128 dimensional descriptor 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191223170837231.png" alt="image-20191223170837231" style="zoom:50%;" />

***<u>在最后，对特征向量进行归一化处理，去除光照变化的影响</u>。***



# Ransac

## A. 核心思想

## B. 优点

## C. 基本步骤

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191223172555445.png" alt="image-20191223172555445" style="zoom:50%;" />

给定的参数是：

* n: 点点数量
* k: 迭代的次数
* t: 可以被认为是内点的范围
* d: 被认为fit well的model需要的内点数

1. 在数据中随机均匀地选择几个点设置为hypothetical inliers
2. 计算拟合inliers的模型
3. 把其他没有选择的点带入模型，计算其是否为内点
4. 记录内点数量
5. 重复上述步骤k次
6. 比较各次拟合模型的内点数量, 选择内点数量最多的模型

# Image Stitching

## A. 步骤

1. detect key points 
2. build the SIFT descriptors 
3. Match SIFT descriptors
4. Fitting the transformation
5. RANSAC
6. Blending 



# 光流

## A. 光流解决的问题

是空间运动物体在观察成像平面上的像素运动的瞬时速度，是利用图像序列中像素在时间域上的变化以及相邻帧之间的相关性来找到上一帧跟当前帧之间存在的对应关系，从而计算出相邻帧之间物体的运动信息的一种方法.

评估从H到I到像素运动，给出图像H中的一个像素， 找到图像I中相同颜色的相近像素，解决的是像素对应问题



## B. 光流的三个基本假设

* brightness constancy 亮度恒定性

$$I(x+u, y+v, t+1) = I(x,y,t)$$

* spatial coherence 空间相干性
* small motion 细微运动





## C. optical flow equation

$$\begin{aligned}0=&I(x+u,y+v)-H(x,y)\\ \approx & I(x,y) +I_x u+I_y u-H(x,y)\;\; Tarlor\\ \approx & (I(x,y)-H(x,y)) +I_x u+I_y u\\ \approx &I_t+uI_x+vI_y \\ \approx &I_t+\nabla I\cdot[u,v]^T\end{aligned} $$







# 立体视觉

双目视觉求解深度



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230163100997.png" alt="image-20191230163100997" style="zoom:40%;" />

<img src="/Users/jones/Downloads/image-20191230163620697.png" alt="image-20191230163620697" style="zoom:40%;" />

$x_r$是负值， $x_l$是正值，两个三角形相似。

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230171631357.png" alt="image-20191230171631357" style="zoom:30%;" />



# 结构光

## 利用结构光获取三位数据的基本原理

一只眼睛一束光

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230182319140.png" alt="image-20191230182319140" style="zoom:50%;" />

可以得到

$tan\theta=\frac{z}{b+x}$

根据三角形相似

$\frac{x}{x'}=\frac{(b+x)tan\theta}{f}$

解得：$x=\frac{b}{fcot\theta-x'}$

<br/>



ICP algorithm

https://www.zhihu.com/question/34170804/answer/121533317







# Egienface

## 1. Covariance

考虑多维随机变量$\mathbf{x}=(x_1, x_2, \cdots, x_d)$

协方差定义为

$cov(x_i, x_j)=E{[x_i-E(x_i)][x_j-E(x_j)]}=E(x_ix_j)-E(x_i)E(x_j)$

#### 协方差的一些性质

* Variance as a measure of the deviation from the mean for points in one dimension

* Covariance from the mean as a measure of how much the dimensions vary with respect to each other 

* Covariance is a relationship between the 2 dimensions 

* The covariance between one dimension and itself is the variance 

#### 协方差的含义：符号

+: ***B<u>oth dimensions increase or decrease together</u>*** e.g. as the number of hours studied increases, the marks in that subject increase.  

−: ***<u>While one increases the other decreases</u>*** , or vice-versa 

0: ***<u>No correlation</u>*** of each other. e.g. heights of students vs the marks obtained in a subject 

### 协方差矩阵covariance matrix

$$c=\left[\begin{matrix}cov(x,x)&cov(x,y)&cov(x,z)\\cov(y,x)&cov(y,y)&cov(y,z)\\cov(z,x)&cov(z,y)&cov(z,z)\end{matrix}\right]$$

#### 性质

* 这是一个对称矩阵，因为$cov(x,y)=cov(y,x)$
* 对角线上就是方差

## 2. PCA principal component analysis

### A.基本思想

用于数据降维。选择一个新的坐标系进行线性降维，使得第一轴上是最大投影方向，第二轴上是第二大投影方向....以此类推



### B. PCA定义

* d-维空间:$\mathbf{x}=(x_1,x_2,\cdots, x_d)$
* 投影方向:$a_1=(a_1^1,a_1^2,\cdots,a_1^d)$ 其中$a_1^Ta_1=1$
* 投影值: $z_1=a_1^tx=\sum_{i=1}^da_1^ix_i$
* 问题：
  * 最大化$var(z1)$
  * 求投影方向$arg\;\underset{a}{max}\;var(z_1)$

### C. PCA求解

$$\begin{aligned}var(z_1)&=E(z_1^2)-[E(z_1)]^2=E[(\sum_{i=1}^da_1^ix_i)^2]-[E(\sum_{i=1}^da_1^ix_i)]^2\\&=\sum_{i,j=1}^da_1^ia_1^jE(x_ix_j)-\sum_{i,j=1}^da_1^ia_1^jE(x_i)E(x_j)\\&=\sum_{i,j=1}^da_1^ia_1^j[E(x_ix_j)-E(x_i)E(x_j)]\\&=\sum_{i,j=1}^da_1^ia_1^jS_{ij}\quad\quad S_{ij}=E(x_ix_j)-E(x_i)E(x_j)\\&=a_1^TSa_1\quad\quad cov(x_i,x_j)=E(x_ix_j)-E(x_i)E(x_j)\end{aligned}$$



我们要最大化$var(z_1)=a_1^TSa_1$  subject to $a_1^Ta_1=1$

可以用拉格朗日乘子法，设$\lambda$为拉格朗日乘子，则转为最大化

$a_1^TSa_1-\lambda(a_1^Ta_1-1)$

对$a_1^T$求微分，得必要条件$Sa_1-\lambda a_1=0\Rightarrow Sa_1=\lambda a_1$

因此

$$var(z_1)=a_1^TSa_1=a_1^T\lambda a_1=\lambda a_1^Ta_1=\lambda$$



为了使$var(z_1)$取得最大值，必须用最大特征值对应的特征向量! 



### Eigenface

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230191544548.png" alt="image-20191230191544548" style="zoom:50%;" />

步骤

* 获得人脸图像的训练集，通常为整个人脸数据库

* 预处理：确定模版，根据人脸两只眼睛的中心位置，缩放平移旋转，使得所有训练人脸图像与模板对其，根据模版，且出脸部区域。对所有人脸图像做灰度值归一化处理

  <img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230192212662.png" alt="image-20191230192212662" style="zoom:50%;" />

  <img src="/Users/jones/Library/Application Support/typora-user-images/image-20191230192248291.png" alt="image-20191230192248291" style="zoom:50%;" />

* 通过PCA计算获得一组特征向量（特征脸）。通常一百个特征向量就足够

* 将每幅人脸图像都投影到由该特征脸张成的子空间中，得到在该子空间坐标

* 对输入的一幅待测图像，归一化后，将其映射到特征脸子空间中。然后用某种距离度量来描述两幅人脸图像的相似性，如欧式距离。