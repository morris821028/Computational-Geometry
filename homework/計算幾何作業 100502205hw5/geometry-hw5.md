title: "計算幾何 - HW05"
date: 2014-12-28 08:56:01
tags: [計算幾何]
categories: [學校課程, 計算幾何]
---
## Chapter 10 ##

### 10.1  ###

In Section 10.1 we solved the problem of finding all horizontal line segments in a set that intersect a vertical segment. For this we used an interval tree with priority search trees as associated structures. There is also an alternative approach. We can use a 1-dimensional range tree on the y-coordinate of the segments to determine those segments whose y-coordinate lies in the y-range of the query segment. The resulting segments cannot lie above or below the query segment, but they may lie completely to the left or to the right of it. We get those segments in O(logn) canonical subsets. For each of these subsets we use as an associated structure an interval tree on the x-coordinates to find the segments that actually intersect the query segment.

a. Give the algorithm in pseudocode.
b. Prove that the data structure correctly solves the queries.
c. What are the bounds for preprocessing time, storage, and query time of this structure? Prove your answers.

給 n 個水平線段，詢問垂直的線段與那些水平線段有相交，輸出所有有相交的線段。

a. Algorithm:
1. 對 n 個線段建造 interval tree，使用課本上 CONSTRUCTINTERVALTREE(l) 的做法。
2. 對 interval tree 上每一個 node 建造 Priority Search Tree，node 依照 x 劃分，分別對 node.x 的左右兩側建造 PST，距離 node.x 越遠的端點，其優先權越高，左右子樹依照 y-value 分兩堆。
3. 詢問調用 QUERYINTERVALTREE(v, qx)，得到相對應的 ndoe 後，查找每個 node 下的 Priority Search tree 與線段相交，調用 QUERYPRIOSEARCHTREE()。

b. Prove that the data structure correctly solves the queries.
上述的作法與 axis-parallel rectangular query window 相同。而此問題的詢問是 axis-parallel rectangular query window 的 subset，一個線段的詢問是 * qx = qx'* 的情況，故得證。

c. What are the bounds for preprocessing time, storage, and query time of this structure? Prove your answers.

preprocessing time : *** O(n log n) ***
store space : *** O(n) ***
query time : *** O(log n + k) ***

前處理因 interval tree 最慘 * T(n) = 2 T(n/2) + O(n) *，建造 PST 只需要 * O(n) *，每個線段最多在兩個 PST 中，得到記憶體空間最多為 * O(n) *。詢問最多為 interval tree 的深度 * O(log n) *

### 10.2  ###
Let P be a set of n points in the plane, sorted on y-coordinate. Show that, because P is sorted, a priority search tree of the points in P can be constructed in * O(n) * time.

類似 bottom0up heap construction，已知 bottom-up heap 可在 O(n) 時間內完成。

假設最後的樹高 H，第 i 次的 bottom-up 會調整 2<sup>H-i</sup> 個元素，每個元素最多下降 i 層。則

{% math-block %}
O(\sum_{i = 1}^{H} i \times 2^{H-i}) = O(1 \times 2^{H-1} + 2 \times 2^{H-2} + ... ) \\
= O((2^{H-1} + 2^{H-2} + ... + 1) + (2^{H-2} + 2^{H-3} + ... + 1) + ...) \\
= O(2^{H} + 2^{H-1} + ... + 1) = O(2^{H+1}) = O(n)
{% endmath-block %}

雖然要求左右子樹的 y 值要符合左小、右大，但由於已經根據 y 排好，bottom-up 時，保證調整的 shiftdown 操作會符合其性質。


### 10.6 ###
Let I be a set of intervals on the real line. We want to be able to count the number of intervals containing a query point in O(logn) time. Thus, the query time must be independent of the number of segments containing the query point.

a. Describe a data structure for this problem based on segment trees, which uses only O(n) storage. Analyze the preprocessing time, and the query time of the data structure.
b. Describe a data structure for this problem based on interval trees. You should replace the lists associated with the nodes of the interval tree with other structures. Analyze the amount of storage, preprocessing time, and the query time of the data structure.
c. Describe a data structure for this problem based on a simple binary search tree. Your structure should have O(n) storage and O(logn) query time. (Hence, segment trees are actually not needed to solve this problem efficiently.)

有 n 個 [l<sub>i</sub>, r<sub>i</sub>]，詢問一個點 x 被多少個區間包含，輸出為一個整數。

a. 使用 segment tree 儲存所有的 interval。

對於其 node(u) 會 cover(l, r) 的區間，紀錄有多少區間 cover(l, r)，最多有 * 2n * 個葉節點，樹節點最多 * 4n * 個，每個 node 只有一個額外的整樹紀錄區間個數。然而一個區間最多被拆分 * O(log n) * 個，依序插入需消耗共計 * O(n log n) * 時間。

preprocessing time : *** O(n log n) ***
query time : *** O(log n) ***
store space : *** O(n) ***

b. 使用 inteerval tree 處理 interval。

對於每個 node 分別對左右兩側建造平衡樹，這個平衡樹要支持某數字是第 k 大或第 k 小的操作。對於一個 node 而言，會有數個區間交 node.x，假設詢問點 qx 在 node.x 左側，則根據左側的平衡樹，查找 qx 是第 k 小，用以回報個數。每一個區間對多在 2 個平衡樹中，故空間複雜度為 * O(n) *。

preprocessing time : *** O(n log n) ***
query time : *** O(log<sup>2</sup> n) ***
store space : *** O(n) ***

c. 使用 simple binary search tree。

1. 將 n 個線段的端點，共計 2n 個丟入 binary search tree。
2. 每一個 node 紀錄有多少個區間包含它，如果 node 是某個線段的右端點，則無視此區間的包含情況。
3. 對於每個詢問 qx，輸出 node = lower_bound(qx) 的包含結果。


將所有端點排序後，會得到 [a<sub>0</sub>, a<sub>1</sub>, a<sub>2</sub>, ..., a<sub>2n</sub>]，每一個 node 的資訊是 [a<sub>i</sub>, a<sub>i+1</sub>) 的區間包含訊息。簡單地說，切成最小塊的相鄰資訊，與 segment tree 反過來的概念。

前處理使用 line sweep algorithm 掃描線算法，來找到每一個端點被多少個線段包含。掃描線算法需要 * O(n log n) *，binary search tree 只需要 * O(n) *，查找 lower_bound 只需要 * O(log n) *，保證最多 2n 個端點，樹最多使用 * O(n) * 個空間。

preprocessing time : *** O(n log n + n) = O(n log n) ***
query time : *** O(log n) ***
store space : *** O(n) ***

### 10.7 ###

a. We want to solve the following query problem: Given a set S of n disjoint line segments in the plane, determine those segments that intersect a vertical ray running from a point (qx,qy) vertically upwards to infinity. Describe a data structure for this problem that uses O(nlogn) storage and has a query time of O(logn+k), where k is the number of reported answers.
b. Now suppose we only want to report the first segment hit by the query ray. Describe a data structure for this problem with O(n) expected storage and O(logn) expected query time. Hint: Apply the locus approach

給定 n 個不相交的線段，詢問一點開始的射線，射線方向向 y 軸正向，輸出所有交到的線段。

a. 使用 segment tree，依照所有的端點的 x 值，接著對於每個 node 建造以 y 值得 binary search tree，儲存數個線段，因為不相交，保證 binary search tree 在 [xl, xr] 區間的所有線段都是單調的。每個線段最多切成 * O(log n) *，故使用空間 * O(n log n) *，詢問 * O(log n + k) *，只須訪問 * O(log n) * 個節點，從 binary tree tree 最遠的開始往下輸出即可。

b. 只找第一個碰到的線段，使用 trapezoidal map 找到 point location，走訪 DG 其最後一個 segment below 的情況就是輸出結果。已知 trapezoidal map 在隨機增量算法中，expected size of search structure * O(n) *，expected query time * O(log n) *。

## Chapter 11 ##

### 11.1 ###

In Chapter 1 we defined the convex hull of a set P of points as the intersection of all convex sets containing the points. In the current chapter we saw another definition: the convex hull of P is the set of all convex combinations of the points in P. Prove that these two definitions are equivalent, that is, prove that a point q is a convex combination of points in P if and only if q lies in every convex set containing P.

首先 convex combinations 指得是 {% math q = \sum_{i = 1}^{n} \lambda_{i} p_{i}, \lambda_{i} \geq 0, \sum_{i = 1}^{n} \lambda_{i} = 1 %}，證明 q 會在所有的凸包集中，也就是 q 點相當於任抓幾點出來，會在這幾點形成的凸包中，公式計算類似物理上的質心計算。

(=>) {% math q = \sum_{i = 1}^{n} \lambda_{i} p_{i}, \lambda_{i} \geq 0, \sum_{i = 1}^{n} \lambda_{i} = 1 %}. Prove q lies in every convex set containing P.

* Base :  
{% math n = 2, q = \lambda_{1} p_{1} + \lambda_{2} p_{2} %}, q lies on segment p<sub>1</sub>p<sub>2</sub>, which must be a subset of every convex set containing p<sub>1</sub>, p<sub>2</sub>.
* Suppose:  
{% math n \le k %} 均成立，當 * n = k + 1 * 時，{% math \lambda_{k+1} = 0 %} 必定成立。  
{% math-block %}
\begin{align}
& q = \lambda_{1} p_{1} + \lambda_{2} p_{2} + ... + \lambda_{k} p_{k} + \lambda_{k+1} p_{k+1} \\
& = (1 - \lambda_{k+1}) \left [ \frac{\lambda_{1}}{1 - \lambda_{k+1}} p_{1} + \frac{\lambda_{2}}{1 - \lambda_{k+1}} p_{2} + ... + \frac{\lambda_{k}}{1 - \lambda_{k+1}} p_{k} \right ] + \lambda_{k+1} p_{k+1} \\
& = (1 - \lambda_{k+1}) ({\lambda_{1}}' p_{1} + {\lambda_{2}}' p_{2} + ... + {\lambda_{k}}' p_{k}) + \lambda_{k+1} p_{k+1} \\
& \text{where } {\lambda_{i}}' = \frac{\lambda_{i}}{1 - \lambda_{k+1}}, \lambda_{1} + \lambda_{2} + ... + \lambda_{k} = 1 - \lambda_{k+1} \\
& \sum_{i = 1}^{k} {\lambda_{i}}' = \sum_{i = 1}^{k} \frac{\lambda_{i}}{1 - \lambda_{k+1}} = \frac{1}{1 - \lambda_{k+1}} \sum_{i = 1}^{k} \lambda{i} = 1
\end{align}
{% endmath-block %}  
Hence, the point {% math {q}' = \sum_{i=1}^{k} {\lambda_{i}}' p_{i}%} is convex combination of p<sub>1</sub>, p<sub>2</sub>, ..., p<sub>k</sub>, q' lies every convex set containing the them.  
{% math-block %}
q = (1 - \lambda_{k+1}) {q}' + \lambda_{k+1} p_{k+1}
{% endmath-block %}  
every convex set containing p<sub>1</sub>, p<sub>2</sub>, ..., p<sub>k</sub>, q'. Since it also contains p<sub>k+1</sub> the set must contain q as a convex combination of two points.  
(<=) Prove q lies in every convex set containing P => {% math q = \sum_{i = 1}^{n} \lambda_{i} p_{i}, \lambda_{i} \geq 0, \sum_{i = 1}^{n} \lambda_{i} = 1 %}  
In particular, q lies in the smallest convex set, the convex hull of P. Triangulate the convex hull, q must lie in one of the triangles {% math \triangle p_{1} p_{2} p_{3} %}. Connect q to p<sub>1</sub>, p<sub>2</sub>, p<sub>3</sub>. This partitions the triangle into tree smaller ones.  
{% math-block %}
\left\{\begin{matrix}
\triangle p_{1} p_{2} p_{3} = A \\
\triangle q p_{2} p_{3} = A_{1} \\ 
\triangle q p_{1} p_{3} = A_{2} \\ 
\triangle q p_{1} p_{2} = A_{3} \\
A = A_{1} + A_{2} + A_{3}
\end{matrix}\right. \Rightarrow q = \frac{A_{1}}{A} p_{1} + \frac{A_{2}}{A} p_{2} + \frac{A_{3}}{A} p_{3}
{% endmath-block%}  
得證。

### 11.2 ###

Prove that the worst case running time of algorithm CONVEXHULL is O(n3), and that there are sets of points where a bad choice of the random permutation makes the algorithm actually need Θ(n3) time.

CONVEXHULL 共有三層 for loop.

* LINE 7 如果每次的新加入的 p<sub>i</sub> 都在 convex hull 外面，即 F<sub>conflict</sub>(p<sub>i</sub>) 非空。
* LINE 10 {% math e \in \pounds %}，而最多有 i - 1 個，投影的情況下，最多 i 個點都在凸包上，因此最多產生 i - 1 個 facets。
* LINE 18 對每個新加入的 facets 最多增加 n - i 個 conflict 點。

故最慘 O(n<sup>3</sup>)。

### 11.6 ###

Describe a data structure that allows you to test whether a query point q lies inside a convex polytope with n vertices in R3. (Hint: Use the results from Chapter 6.)

快速判斷一個點是否在 3D convex hull 中。

* 方案一：3D point location by 3D trapezoidal map. 感覺非常難做，弄出很多柱狀體。
* 方案二：convex hull 最多會有 3n - 6 個面，最多有 3n - 6 個面不等式，判斷是否全在同一側 * O(n) *。
* 方案三：將做好的 3D convex hull，將所有點投影到 x-y 平面，每一個梯形區域會由 2 個 convex hull 的面覆蓋，要不沒有面。對於投影的 2D 建造 trapezoidal map。query 一個點 q 時，先投影到 x-y 平面，找到所在的梯形區域，針對兩面的不等式進行判斷。預處理 * O(n log n) *，詢問 * O(log n) *。

### 11.8 ###

Describe a randomized incremental algorithm to compute the intersection of half-planes, and analyze its expected running time. Your algorithm should maintain the intersection of the current set of half-planes. To figure out where to insert a new half-plane, maintain a conflict graph between the vertices of the current intersection and the half-planes that are still to be inserted.

* 維護半平面 hi 與 Sj 的相交資訊。

* `add half-place`  
繞外部 (半平面的另一側的凸包邊) 的 edge e，將 h<sub>conflict</sub>(e) 移除掉 intersection(e, h<sub>conflict</sub>(e)) in {% math \bar{h_{i}} %}。  
期望值的複雜度依賴中間過程中產生的交集點個數。

* 假設 c(H, h) 表示 inter(H) 和 h 的 conflict vertice 個數。  
則 {% math \sum_{i = 1}^{n} \left [ \frac{2}{i} \sum_{h \in S \setminus S_{i} }{c(H,  h)} \right ] %}－所有的花費。  
{% math-block %}
E[c(S_{i}, h_{i})] = \frac{1}{n-i} \sum_{h \in S \setminus S_{i} } c(S_{i}, h) \\
\Rightarrow \sum_{i = 1}^{n} \left ( \frac{2}{i} \sum_{h \in S \setminus S_{i} }{c(H,  h)} \right ) = \sum_{i = 1}^{n} \left ( \frac{2}{i} \sum_{h \in S \setminus S_{i} }{E(S_{i},  h_{i-1})} \right ) \\
= \sum_{i = 1}^{n} \left ( \frac{2}{i} (n - i) E(S_{i},  h_{i-1}) \right ) = \sum_{i = 1}^{n} \left ( \frac{2(n-i)}{i} E[\text{# of vertices destroy ed at i+1}] \right )
{% endmath-block %}  
對於每一個 vertice v 被創建的時間為 tc(v), 被移除的時間 td(v)。  
{% math-block %}
\sum_{i = 1}^{n} \left ( \frac{2(n-i)}{i} E[\text{# of vertices destroy ed at i+1}] \right ) \\
= \sum_{v} \frac{2(n - td(v) + 1)}{td(v) - 1} \le \sum_{v} \frac{2(n - tc(v)) + 2}{tc(v)} \\
\le \sum_{n}^{i=1} \frac{2(n - i) + 2}{i} E[\left |v \mid tc(v) = i \right |] \\
= \sum_{n}^{i=1} \frac{2(n - i) + 2}{i} \times 2
= O(\sum_{n}^{i=1} \frac{n}{i} - 1) = O(n ln n)
{% endmath-block %}  
得證。