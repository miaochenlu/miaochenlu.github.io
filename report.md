## SIFT

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

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219111629345.png" alt="image-20191219111629345" style="zoom:50%;" />

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

方向直方图的峰值则代表了该特征点处邻域梯度的方向，以直方图中最大值作为该关键点的主方向。为了增强匹配的鲁棒性，只保留峰值大于主方向峰值80％的方向作为该关键点的辅方向。因此，对于同一梯度值的多个峰值的关键点位置，在相同位置和尺度将会有多个关键点被创建但方向不同。仅有15％的关键点被赋予多个方向，但可以明显的提高关键点匹配的稳定性。

### D. 关键点特征描述

通过以上步骤，对于每一个关键点，拥有三个信息：***位置、尺度以及方向***。接下来就是为每个关键点建立一个***描述符***，用一组向量将这个关键点描述出来，使其不随各种变化而改变，比如光照变化、视角变化等等。这个描述子不但包括关键点，也包含关键点周围对其有贡献的像素点，并且描述符应该有较高的独特性，以便于提高特征点正确匹配的概率。

- Divide the 16x16 window into a 4x4 grid of cells (2x2 case shown below) 
- Compute an orientation histogram for each cell 
- 16 cells * 8 orientations = 128 dimensional descriptor 

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219114435596.png" alt="image-20191219114435596" style="zoom:50%;" />



## RANSAC

We have found point matches between two images, and we think they are related by some parametric transformation (e.g. translation; scaled Euclidean; affine). How do we estimate the parameters of that transformation? 

**Outlier**

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219115215698.png" alt="image-20191219115215698" style="zoom:50%;" />

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191219115312299.png" alt="image-20191219115312299" style="zoom:50%;" />







# 实现

## A. get the transformation

`BFmatcher`

它取第一个集合里一个特征的描述子并用第二个集合里所有其他的特征和他通过一些距离计算进行匹配。最近的返回。

`cv2.drawMatches(imageA, kpsA, imageB, kpsB, matches[:10], None, flags=2) `

对两个图像关键点进行连线操作

参数说明：imageA和imageB表示图片，kpsA和kpsB表示关键点， matches表示进过`cv2.BFMatcher`获得的匹配的索引值，也有距离， flags表示有几个图像



```python
def getGoodMatch(img1, img2): 
    sift = cv2.xfeatures2d.SIFT_create()
    keypoints1, descriptor1 = sift.detectAndCompute(img1, None)
    keypoints2, descriptor2 = sift.detectAndCompute(img2, None)

    bf = cv2.BFMatcher()
    matches = bf.knnMatch(descriptor1, descriptor2, k = 2)
    pointsGood = []
    matchGood = []
    for m, n in matches:
        if m.distance < 0.8 * n.distance:
            pointsGood.append([m])
            matchGood.append(m)
    img3 = cv2.drawMatchesKnn(img1, keypoints1, img2, keypoints1, pointsGood[:20], None, flags = 2)
    cv2.imshow("match", img3)
    
    if len(matchGood) > 10:
        img1kp = np.float32([keypoints1[m.queryIdx].pt for m in matchGood]).reshape(-1,1,2)
        img2kp = np.float32([keypoints2[m.trainIdx].pt for m in matchGood]).reshape(-1,1,2)

        M, mask = cv2.findHomography(img2kp, img1kp, cv2.RANSAC, 5.0)
    return M
```



## B. stitching and blending

新建一个dst矩阵，将变换过后的imgcolor2图像[右侧图像[拷贝到矩阵中，然后将imgcolor1图像[左侧图像]拷贝到矩阵中

同时，也做了blending的操作。这里我们blending的方法是

> 图像各自保留自己独有的区域
>
> 图像交叉的区域做一个混合 `dst[i, j, k] = img1color[i, j, k] + (1 - ratio) * img2[i, j, k]`

```python
def warp(img1color, img2color, M):
    height = img1color.shape[0]
    widthImg1 = img1color.shape[1]
    widthImg2 = img2color.shape[1]
    dst = cv2.warpPerspective(img2color, M, (img2color.shape[1] + img1color.shape[1], img1color.shape[0]))
    ratio = 0.5

    for i in range(img1color.shape[0]):
        flag = 0
        for j in range(img1color.shape[1]):
            if dst[i, j + 3, 0] != 0 and img1color[i, j, 0] != 0:
                if flag == 0 or flag == 2 or flag == 3:
                    dst[i, j, 0] = img1color[i, j, 0]
                    dst[i, j, 1] = img1color[i, j, 1]
                    dst[i, j, 2] = img1color[i, j, 2]
                dst[i, j, 0] = ratio * img1color[i, j, 0] + (1 - ratio) * dst[i, j, 0]
                dst[i, j, 1] = ratio * img1color[i, j, 1] + (1 - ratio) * dst[i, j, 1]
                dst[i, j, 2] = ratio * img1color[i, j, 2] + (1 - ratio) * dst[i, j, 2]
                flag += 1
                pass
            else:
                dst[i, j, 0] = img1color[i, j, 0]
                dst[i, j, 1] = img1color[i, j, 1]
                dst[i, j, 2] = img1color[i, j, 2]

    rows, cols = np.where(dst[:, :, 0] != 0)
    min_row, max_row = min(rows), max(rows) + 1
    min_col, max_col = min(cols), max(cols) + 1
    fdst = dst[min_row:max_row, min_col:max_col, :]
    cv2.imwrite("stitch.jpg", fdst)
    return fdst
```







# 实验结果

## 1. Matching

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221234041220.png" alt="image-20191221234041220" style="zoom:40%;" />

## 2. Before blending



<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221234318129.png" alt="image-20191221234318129" style="zoom:50%;" />

## 3. after blending

<img src="/Users/jones/Library/Application Support/typora-user-images/image-20191221232940624.png" alt="image-20191221232940624" style="zoom:50%;" />





# 实验心得

可以发现在blending之后，光线方面的差别确实变小了，但是图像的拼接处出现了缝隙。

这是因为在tu xia