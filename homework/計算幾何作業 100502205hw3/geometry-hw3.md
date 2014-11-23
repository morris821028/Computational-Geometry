title: "計算幾何 - HW03"
date: 2014-10-26 06:49:32
tags: [計算幾何]
categories: [學校課程, 計算幾何]
---

## Master Theorem ##

{% math-block %}
T(n) = 
\begin{cases}
aT(n/b) + n^{c} & \text{ if } n > 1 \\
d & \text{ if } n = 1
\end{cases}
{% endmath-block %}

1. {% math \text{if } log_{b}a < c, T(n) = \theta(n^{c})%}
2. {% math \text{if } log_{b}a = c, T(n) = \theta(n^{c} log n)%}
3. {% math \text{if } log_{b}a > c, T(n) = \theta(n^{log_{b}a})%}

## Extend Master Theorem ##

{% math-block %}
T(n) = 
\begin{cases}
aT(n/b) + f(n) & \text{ if } n > 1 \\
d & \text{ if } n = 1
\end{cases} \\

E = log_{b}(a)
{% endmath-block %}

1. {% math \text{if } f(n) = O(n^{E} (log_{b}n)^{\alpha} ) \text{ and } \alpha < -1, T(n) = \theta(n^{E})%}
2. {% math \text{if } f(n) = O(n^{E} (log_{b}n)^{\alpha} ) \text{ and } \alpha = -1, T(n) = \theta(n^{E} log_{b} log_{b}(n))%}
3. {% math \text{if } f(n) = O(n^{E} (log_{b}n)^{\alpha} ) \text{ and } \alpha > -1, T(n) = \theta(n^{E}(log_{b}n)^{\alpha + 1})%}

# Chapter 5 #

## 5.1 ## 

In the proof of the query time of the kd-tree we found the following
recurrence:
{% math-block %}
Q(n) = 
\begin{cases}
O(1) & \text{ if } n = 1 \\
2 + 2Q(n/4)& \text{ if } n > 1
\end{cases}
{% endmath-block %}
Prove that this recurrence solves to Q(n) = O(√n). Also show that Ω(√n) is a lower bound for querying in a kd-tree by defining a set of n points and a query rectangle appropriately.

-----

1. by master theorem, {% math a = 2, b = 4, c = 0 \Rightarrow Q(n) = \Theta(\sqrt{n}) %}
2. {% math \Omega(\sqrt{n}) %} 是 kd-tree 的 lower_bound。如果 + 號存在的行都一直被執行，另外 - 號行都不會被執行，那麼複雜度就會達到 {% math \Omega(\sqrt{n}) %}，要明白如何加上 report 則會更慢，必須將包含的點依序回報。找一個不包含所有點的細長矩形於正中央即可，每次循環切割到 x 時，保證會留下 n/4 個節點。

{% codeblock %}
Algorithm: SEARCHKDTREE(v, R) 
if v is leaf
	report the points stored at v if it lies in R.
else
-	if region(lc(v)) contains R
		report subtree lc(v)
+	else if lc(v) intersects R
		SEARCHKDTREE(lc(v), R)
  
-	if region(rc(v)) contains R
		report subtree rc(v)
+ 	else if rc(v) intersects R
		SEARCHKDTREE(rc(v), R)
{% endcodeblock %}  

## 5.3 ## 

In Section 5.2 it was indicated that kd-trees can also be used to store sets of points in higher-dimensional space. Let P be a set of n points in d-dimensional space. In parts a. and b. you may consider d to be a constant.

1. Describe an algorithm to construct a d-dimensional kd-tree for the points in P. Prove that the tree uses linear storage and that your algorithm takes {% math O(n log n) %} time.
2. Describe the query algorithm for performing a d-dimensional range query. Prove that the query time is bounded by {% math O(n^{1−1/d} +k) %}.
3. Show that the dependence on d in the amount of storage is linear, that is, show that the amount of storage is {% math O(dn) %} if we do not consider d to be a constant. Give the dependence on d of the construction time and the query time as well.

-----

{% codeblock %}
Algorithm : build(int k, int l, int r, int dep)
	if l > r
		return NULL
	m = (l + r)/2
	Node ret = median axis[k%K] of point[l, r] ----- O(n)
	split points[l, r] by median               ----- O(n), C++ std::nth_element()
	ret.lson = build((k+1)%K, l, m-1);
	ret.rson = build((k+1)%K, m+1, r);
	return ret;
{% endcodeblock %}

1. 利用 median of medians 算法找到中位數，數據儲存用指針來完成搜索，不用移動資料。  
以上代碼根據遞迴公式 {% math T(n) = 2T(n/2) + O(n) \Rightarrow T(n) = \Theta(n log n)%}，而 kd-tree 是一個 full binary tree，每一個節點都代表一個實際資料，因此空間複雜度 {% math O(n) %} 
2. 假設在 d 為空間，Q(n) 表示 n 個點的詢問，依序按照維度切割 (ex. x, y, z, x ...)，現在只關注 x 軸上的切割，假設詢問範圍包含所有的 y, z，那麼在 2ed x 節點中，每一個節點具有 {% math n/2^{d} %} 資料，而同一層會有 {% math 2^{d-1} %} 個 x 維度的子節點。然後遞迴詢問 {% math 2^{d-1} %} 所包含的範圍查找。  
藉由 master theorem, {% math a = 2^{d-1}, b = 2^{d}, c = 0 \Rightarrow Q(n) = \Theta(n^{1 - 1/d})%}  
{% math-block %}
Q(n) =
\begin{cases}
O(1) & \text{ if } n < 2^{d} \\
2^{d-1} + 2^{d-1} Q(n/2^{d}) & \text{ if } n \geq 2^{d}
\end{cases}
{% endmath-block %}
3. 如果 d 不是常數，每一個內部節點空間 O(d)，有 n 個節點則需 O(dn) 的儲存空間。詢問上，需要在 intersected 花 O(d) 時間決定是否存在交集、包含，再來判斷是否走訪子樹，因此詢問是 {% math 2dQ(n) = O(dn^{1-1/d}) %}，加上回報的效率為 {% math 2dQ(n) = O(dn^{1-1/d} + k) %}。

## 5.5 ##

Algorithm SEARCHKDTREE can also be used when querying with other ranges than rectangles. For example, a query is answered correctly if the range is a triangle.

a. Show that the query time for range queries with triangles is linear in the worst case, even if no answers are reported at all. Hint: Choose all points to be stored in the kd-tree on the line y = x.
b. Suppose that a data structure is needed that can answer triangular range queries, but only for triangles whose edges are horizontal, vertical, or have slope +1 or −1. Develop a linear size data structure that answers such range queries in O(n3/4+k) time, where k is the number of points reported. Hint: Choose 4 coordinate axes in the plane and use a 4-dimensional kd-tree.
c. Improve the query time to O(n2/3+k). Hint: Solve Exercise 5.4 first.

-----

1. 最慘情況是 O(n)－詢問範圍為三角形。  
詢問的範圍如圖，所有點落在 y = x 上，三角形範圍是一個很貼近 y = x 的三角形，三角形並沒有包含任何一個點，卻與所有劃分的區域有交集，因此必須走訪所有節點。
2. 如果要支持三角形範圍查找 (三角形的邊要不垂直、平行、斜率 +1、斜率 -1)。找到一個詢問 {% math O(n^{3/4} + k)%} 的資料結構。  
類似 kd-tree，但是輪替的標準為 ** x 軸 **、** y 軸 **、** 斜率 1 **、** 斜率 -1 **，根據 5.3(b) 的公式，相當於 d = 4 的情況，得到 {% math O(n^{1 - 1/4} + k) = O(n^{3/4} + k)%}。
3. 加快到 {% math O(n^{2/3} + k)%} 的詢問時間。  
在這裡想到是建立兩個 kd-tree，其中一個順序為 ** x 軸 **、** 斜率 1 **、** 斜率 -1 **，另一個順序為 ** y 軸 **、** 斜率 1 **、** 斜率 -1 **。前者可以回答向上、向下三角形，後者回答向左、向右三角形。相當於 d = 3 的切割，最多拆成 4 個範圍查找，{% math O(4n^{1 - 1/3} + k) = O(n^{2/3} + k)%}。
	1. 在詢問矩形時，拆成四個三角形查找 (向上、向下、向左、向右三角形)
	2. 在詢問三角形時，拆成兩個三角形查找

## 5.9 ##

One can use the data structures described in this chapter to determine whether a particular point (a,b) is in a given set by performing a range query with range [a : a]×[b : b]. 

1. Prove that performing such a range query on a kd-tree takes time O(logn).
2. What is the time bound for such a query on a range tree? Prove your answer.

-----

1. 對於 kd-tree 所消耗的時間 O(log n) 說明。  
{% math [a:a] \times [b:b] %} 並不會跨區間。在 SEARCHKDTREE(v, R) 函數中，Line SEARCHKDTREE(lc(v), R) 和 SEARCHKDTREE(rc(v), R) 只會有一個符合。
{% math-block %}
Q(n) =
\begin{cases}
O(1) & \text{ if } n = 1 \\
1 + Q(n/2) & \text{ if } n > 1
\end{cases}
{% endmath-block %}
2. 對於 kd-tree 所消耗的時間 O(d log n) 說明。  
首先能知道會先在第一維度探訪 {% math [a:a] %} 的葉節點，中間經過 log n 個節點最後到葉節點，然後在其相對應的 y 軸 tree 中查找 {% math [b:b] %} 也是 log n。因此是 {% math O(\sum{i = 1}^{d} log n) = O(d log n) %}

## 5.11 ##

Let S1 be a set of n disjoint horizontal line segments and let S2 be a set
of m disjoint vertical line segments. Give a plane-sweep algorithm that
counts in O((n+m) log(n+m)) time how many intersections there are
in S1 ∪ S2.

-----

給 n 個不相交的水平線段、m 個不相交的垂直線段，在 O((n+m) log (n+m)) 的時間內找到焦點個數。

{% codeblock %}
Algorithm:
	1. sort all x-coordinate ----- O((n+m) log (n+m))
	2. sweep x from small to large, maintain a segment tree or range tree.

	for x = -oo to oo
		for each horizontal line (sx, y)-(ex, y) in x
			if sx = x
				1Drangetree.add(y) 				----- O(log (n+m))
		for each vertical line (x, sy)-(x, ey) in x
			ret += 1Drangetree.query(sy, ey) 	----- O(log (n+m))
		for each horizontal line (sx, y)-(ex, y) in x
			if ex = x
				1Drangetree.remove(y) 			----- O(log (n+m))
	return ret
{% endcodeblock %}

# Chapter 6 #

## 6.1 ##

Draw the graph of the search structure D for the set of segments depicted
in the margin, for some insertion order of the segments.

-----

雖然沒有加入順序的考量，但是手爆一個 17 個線段、平面空間 29 個的建造 ... 也許有點瘋狂，用最簡單的由上而下掃描，造成一個傾斜的樹也是各種不容易。

## 6.2 ## 

Give an example of a set of n line segments with an order on them that
makes the algorithm create a search structure of size Θ(n2) and worst-case
query time Θ(n).

-----

找到一個最慘空間 {% math \Theta(n^{2}) %}，最慘詢問時間 {% math \Theta(n) %}。

n 個線段，將其中 n/2 放置在同一個水平線上，對於剩餘 n/2 個：

每次加入的順序 s1, s2, ..., si，每次的線段的 x 值會包含前一個線段，{% math s_{i}.lx < s_{i-1}.lx, s_{i}.rx > s_{i-1}.rx %}，美加入一個線段，會增加 n/2 個內部節點，並且最大深度增加 1。總計加入 n/2 次，增加的節點數量 O(n/2 x n/2) = O(n^2)，深度 O(n/2) = O(n)。

## 6.5 ##

Given a convex polygon P as an array of its n vertices in sorted order along the boundary. Show that, given a query point q, it can be tested in time O(logn) whether q lies inside P.

-----

由於詢問次數相當多，必須將複雜度降到 O(log n)，採用的方式將凸包其中一個點當作基準，則如果在凸包的點而言，一定會落在某個以基點拉出的三角形內部中，為了方便計算，則可以拿外積的正負邊界上。得到可能的三角形後，從邊界中可以藉由外積計算是否同側。

採用射線法 O(n) 肯定是吃大虧的。

{% codeblock part of UVa 12048 - Inhabitants %}
double cross(Pt o, Pt a, Pt b) {
    return (a.x-o.x)*(b.y-o.y)-(a.y-o.y)*(b.x-o.x);
}
int inside_convex(const Pt &p, Pt ch[], int n) {
	if(n < 3)
        return false;
	if(cross(ch[0], p, ch[1]) > eps)
        return false;
	if(cross(ch[0], p, ch[n-1]) < -eps)
        return false;
    
	int l = 2, r = n-1;
	int line = -1;
	while(l <= r) {
		int mid = (l + r)>>1;
		if(cross(ch[0],p, ch[mid]) > -eps) {
			line = mid;
			r = mid - 1;
		} else l = mid + 1;
	}
	return cross(ch[line-1], p, ch[line]) < eps;
}
{% endcodeblock %}