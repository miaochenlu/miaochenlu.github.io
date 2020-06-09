# Computational Geometry

# Convex Hull

`definition`{:.warning}

A set of points S is convex if $p,q\in S$, segment pq is in $S$

<img src="../../assets/images/image-20200604130815691.png" alt="image-20200604130815691" style="zoom:50%;" />

二维点集Q的凸包(convex hull)为

* intersection of all convex sets that contain Q

* intersection of all halfspaces that contains Q

* the smallest convex polygon P that contains Q(这个比较好理解)

* union of all the triangles determined by points in Q



## Convex Hull Problem

给定 $Q=\{p_1,p_2,\cdots,p_n\}$,  从Q中找到属于Q的convex hull的顶点 $CH(Q)$

* Input: $Q=\{p_1,p_2,\cdots,p_n\}$
* Output: $CH(Q)=\{p_8,p_5,p_4,p_9,p_2\}$

<img src="../../assets/images/image-20200604131537253.png" alt="image-20200604131537253" style="zoom:20%;" /><img src="../../assets/images/image-20200604131555297.png" alt="image-20200604131555297" style="zoom:30%;" />

接下来我们通过problem reduction来推导Lower Bound



## Lower Bound for CH Problem

计算n个二维点的凸包需要$\Omega(nlogn)$

事实上有: Distinct Integer Soring$\propto$ Convex Hull Problem

给定n个数$a_1,\cdots,a_n$, 可以生成n个点$(a_i,a_i^2)$

<img src="../../assets/images/image-20200604140225307.png" alt="image-20200604140225307" style="zoom:20%;" />

Basic geometric fact:

{:.success}

All these points appear on their convex hull in sorted order by x coordinate.

<img src="../../assets/images/image-20200604140343307.png" alt="image-20200604140343307" style="zoom: 33%;" />

步骤

1. Compute the new points $(a_i,a_i^2)$
2. Compute the convex hull of the n points in $T(n)$ time
3. Find the left-most point in the convex hull
4. List the points in anti-clockwise order

步骤1，3，4需要$O(n)$时间，因此总时间要$T(n)+O(n)$

因为sorting需要$\Omega(nlogn)$, 因此$T(n)$必须也为$\Omega(nlogn)$



## Divide and Conquer

给定n个点$p_1,p_2,\cdots,p_n$

$ConvexHull(p_1,p_2,\cdots,p_n)$

Find the midian of $\{p_1,p_2,\cdots,p_n\}$ based on x-coordinates

Divide the  $\{p_1,p_2,\cdots,p_n\}$ into 2 equal size sets, $\{p_1,p_2,\cdots,p_{n/2}\}$, $\{p_{n/2+1},p_{n/2+2},\cdots,p_n\}$

1. Find ConvexHull$(p_1,p_2,\cdots,p_{n/2})$
2. Find ConvexHull($p_{n/2+1},p_{n/2+2},\cdots,p_n)$
3. Combine to get the overall hull.

怎么combine呢

给定两个凸多边形P和Q, P完全在Q左侧

在$O(n)$时间找到$P\cup Q$的convex hull, 其中n是P,Q的corner数量



### Merge in $O(n)$ time

Find upper and lower tangents in O(*n*) time (to be proved later)

Compute the convex hull of $A\cup B$: 

<img src="../../assets/images/image-20200604142622444.png" alt="image-20200604142622444" style="zoom:20%;" /><img src="../../assets/images/image-20200604142701115.png" alt="image-20200604142701115" style="zoom:20%;" />

* Starting with right endpoint of  lower tangent, walk anti-clockwise around the convex hull of B

<img src="../../assets/images/image-20200604142807730.png" alt="image-20200604142807730" style="zoom: 25%;" />

* When hitting the right endpoint of the upper tangent, cross over to the convex hull of B 

<img src="../../assets/images/image-20200604142902320.png" alt="image-20200604142902320" style="zoom:20%;" />



* Walk anti-clockwise around the convex hull of A

<img src="../../assets/images/image-20200604143007352.png" alt="image-20200604143007352" style="zoom:20%;" /><img src="../../assets/images/image-20200604143039704.png" alt="image-20200604143039704" style="zoom:20%;" />

* When hitting left endpoint of the lower tangent, 
   follow the lower tangent to its left endpoint

This takes O(*n*) time



那么问题又来了，怎么找到lower tangent呢？

### Finding the lower tangent

Q: When is line $l$ tangent to a polygon?

A: Assume $l$ is tangent at point $p_i$, then slope($l$) must be in-between of slope $(p_{i-1},p_i)$ and slope$(p_i,p_{i+1})$

<img src="../../assets/images/image-20200604143646653.png" alt="image-20200604143646653" style="zoom:50%;" />

Lower common tangent of A and B

* a - rightmost point of A
* b - leftmost point of B
* Let $T=(a,b)$

```pascal
while T not lower tangent do {
    while T not lower tangent to B
    	do { b = b + 1}
    while T not lower tangent to A
    	do {a = a - 1}
}
```

<img src="../../assets/images/image-20200604143931629.png" alt="image-20200604143931629" style="zoom: 20%;" /><img src="../../assets/images/image-20200604144011870.png" alt="image-20200604144011870" style="zoom:20%;" />

<img src="../../assets/images/image-20200604144041213.png" alt="image-20200604144041213" style="zoom:20%;" /><img src="../../assets/images/image-20200604144105747.png" alt="image-20200604144105747" style="zoom:20%;" />

<img src="../../assets/images/image-20200604144128038.png" alt="image-20200604144128038" style="zoom:20%;" /><img src="../../assets/images/image-20200604144153196.png" alt="image-20200604144153196" style="zoom:20%;" />

Time Complexity: $O(n)$ ?为啥

### Runtime

1.Find the median node by *x*-coordinate

2.Split the nodes into two halves.

3.Compute the convex hulls of both halves

4.Find the two tangents of the 2 convex hulls

5.Re-number the nodes in the overall convex hull (clockwise around the polygon).

* step1 and 2: $O(n)$时间
* step4 and 5: $O(m)$时间，m是当前实例的规模(最坏是$O(n)$)
* step3: $2T(\frac{n}{2})$时间

$$T(n)=2T(\frac{n}{2})+O(n)$$

因此, $T(n)=O(nlog\;n)$



## Jarvis March - gift wrapping

1.从$p_0$开始，$p_0$是在convex hull中的一个点，比如说是lowerest point

2.从$i=0$开始，loop直到又回到$p_0$， 选择$p_{i+1}$使得所有点都在直线$p_ip_{i+1}$一侧



<img src="../../assets/images/image-20200609101914348.png" alt="image-20200609101914348" style="zoom: 20%;" /><img src="../../assets/images/image-20200609102109447.png" alt="image-20200609102109447" style="zoom:20%;" /><img src="../../assets/images/image-20200609101955667.png" alt="image-20200609101955667" style="zoom:20%;" /><img src="../../assets/images/image-20200609102014913.png" alt="image-20200609102014913" style="zoom:20%;" /><img src="../../assets/images/image-20200609102029573.png" alt="image-20200609102029573" style="zoom:20%;" />

### Time Complexity

step1: $O(n)$ 遍历所有点找lowerest point

step2中每个loop需要$O(n)$来找$p_{i+1}$

Total time = $O(nh)$ 

其中h是在CH中的点的个数



## Graham Scan

1.找到y坐标最小的点p

2.根据$(p',p)$的斜率从小到大对p进行排序

3.根据排序考虑每个点Q, 根据他前面两个点判断这是一个left turn还是right turn

4.如果是right turn, 就忽略掉造成问题的点

5.这个过程会回到p并且逆时针输出CH

<img src="../../assets/images/image-20200609110420312.png" alt="image-20200609110420312" style="zoom:25%;" /><img src="../../assets/images/image-20200609110441291.png" alt="image-20200609110441291" style="zoom:25%;" /><img src="../../assets/images/image-20200609110454950.png" alt="image-20200609110454950" style="zoom:25%;" /><img src="../../assets/images/image-20200609110511498.png" alt="image-20200609110511498" style="zoom:25%;" /><img src="../../assets/images/image-20200609110712671.png" alt="image-20200609110712671" style="zoom:25%;" /><img src="../../assets/images/image-20200609110725840.png" alt="image-20200609110725840" style="zoom:25%;" /><img src="../../assets/images/image-20200609110805866.png" alt="image-20200609110805866" style="zoom:25%;" />



### Left turn or Right turn

Given $P_1(x_1,y_1), P_2(x_2,y_2)$, is $P_1$ is clockwise or anti-clockwise from $P_2$ with respect to origin?

计算$P_1,P_2$的叉积 $P_1\times P_2=x_1y_2-x_2y_1$

* $P_1\times P_2>0$, $P_1$ is clockwise  from $P_2$ with respect to origin

* $P_1\times P_2<0$, $P_1$ is anti-clockwise  from $P_2$ with respect to origin

<img src="../../assets/images/image-20200609105330787.png" alt="image-20200609105330787" style="zoom:40%;" />



### Time Complexity

1.Sorting the points - $O(nlog\;n)$

2.Each point is scanned - $O(n)$

* 如果是left turn， 那么这次操作的cost就被记录在到达的端点上

<img src="../../assets/images/image-20200609174105195.png" alt="image-20200609174105195" style="zoom:25%;" /><img src="../../assets/images/image-20200609174130965.png" alt="image-20200609174130965" style="zoom:25%;" />

* 如果是right turn, 那么这次操作的cost就记录在引起right turn的点上

<img src="../../assets/images/image-20200609174155984.png" alt="image-20200609174155984" style="zoom:25%;" /><img src="../../assets/images/image-20200609174210379.png" alt="image-20200609174210379" style="zoom:25%;" />

* 那么每个点最多被charge twice

因此Time complexity $O(n\;logn)$



## Quickhull

1.找到Q中的两个点，一个是最低点，一个是最高点

2.用这两个点形成的线将这些点分成两个子集

<img src="../../assets/images/image-20200609175223391.png" alt="image-20200609175223391" style="zoom:25%;" />

3.这直线的一边，找到距离直线最远的点。另一边也同样

<img src="../../assets/images/image-20200609182302754.png" alt="image-20200609182302754" style="zoom:25%;" /><img src="../../assets/images/image-20200609182318880.png" alt="image-20200609182318880" style="zoom:25%;" />

4.重复上述步骤直到所有点都被包含

<img src="../../assets/images/image-20200609182556462.png" alt="image-20200609182556462" style="zoom:25%;" />

## Line Segment Intersections

Given: n line segments defined by 2n endpoints

目标：计算所有的intersections

Brute force approach:

* Check each line segment for intersection with every other line segment
* Requires $O(*n*2)$ time

Not optimal if many pairs do not intersect



#### Plane Sweep Algorithm

***sweeping line***: Moves a line across the plane to find intersection

在sweeping line左边的intersections都被计算了。

***status of sweeping line***: set of segments currently intersecting it.

Sweeping line stops to update its status at ordered stopping points:

* Endpoints of line segments to the right of sweeping line
* Intersection points to the right of the sweep line for currently adjacent line segments

<br>



<img src="../../assets/images/image-20200609195005545.png" alt="image-20200609195005545" style="zoom: 33%;" />

<img src="../../assets/images/image-20200609195048727.png" alt="image-20200609195048727" style="zoom: 33%;" />

<img src="../../assets/images/image-20200609195143733.png" alt="image-20200609195143733" style="zoom: 33%;" />

`Observation: `{:.warning}

{:.warning}

Two line segments cannot intersect unless they are next to each other – only test neighbors on sweeping line for intersection



#### Data Structure Requirements

* Status data structure
  * Insertion
  * Deletion
  * Finding neighbors of a line segment

Binary search tree in $O(log \;n)$ time for all operations

* Stopping point data structure
  * Point with the minimum x-coordinate to be removed 
  * A new point inserted 
  * Intersection point to be deleted 
     (when two line segments are no longer neighbors)

Priority list (Heap) in $O(log \;n)$ time for all operations

