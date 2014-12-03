title: "計算幾何 - HW04"
date: 2014-11-28 08:39:44
tags: [計算幾何]
categories: [學校課程, 計算幾何]
---

## Chapter 7 ##

### 7.1  ###

Prove that for any n > 3 there is a set of n point sites in the plane such that one of the cells of Vor(P) has n−1 vertices

證明一個點個數 n > 3 的 Voronoi diagram，存在一個 cell 具有 n - 1 個頂點。

![7.1](http://i.imgur.com/86AoEWl.png)

如圖所示，一個點放置在圓心，剩餘的 n - 1 個放置在圓周上。則中間的 cell 必然有 n - 1 個頂點。

### 7.3 ###

Show that {% math \Omega (nlogn) %} is a lower bound for computing Voronoi diagrams by reducing the sorting problem to the problem of computing Voronoi diagrams. You can assume that the Voronoi diagram algorithm should be able to compute for every vertex of the Voronoi diagram its incident edges in cyclic order around the vertex.

![7.3](http://i.imgur.com/Algk5Xi.png)

證明 Voronoi diagram 的 lower bound {% math \Omega (nlogn) %}，藉由 reduce 到 sorting problem。

> 關於 reduce 證明，將一個簡單、約束較少的問題 A，藉由一個合適的轉換，變成一個困難問題 B 的 subset，已知 A 的 lower bound，則 B 的 lower bound 至少與 A 相同。

1. 假設排序 n 個整數 {% math x_{1}, x_{2}, ..., x_{n} %} 
2. 轉換成 {% math (x_{1}, 0), (x_{2}, 0), ..., (x_{n}, 0) %} 將所有點放置在 x 軸上，並且額外增加一點 {% math (\infty, 0) %}。將 n + 1 個點找到 Voronoi diagram，對於 {% math (\infty, 0) %} 的 cell 而言，恰好邊都是由另外 n 個點對應的 cell 構成 cell edge (相鄰)。假設儲存邊的順序為順或逆時針，則邊的順序等價排序結果。

最後如上圖所示，又已知轉換需要 {% math O(n)%}，sorting {% math \Omega (nlogn) %}，則 Voronoi diagram 的 lower bound {% math \Omega (nlogn) %}。

### 7.5 ###

Give an example where the parabola defined by some site {% math p_{i} %} contributes more than one arc to the beach line. Can you give an example where it contributes a linear number of arcs?

![7.5](http://i.imgur.com/VEwhdjt.png)

如上圖所示，{% math p_{i} %} 拉出的拋物線 (parabola) 有可能提供多個弧 (arc)。(最左側的點提供 2 個弧)

一個點 {% math p_{i} %} 存有的 arc 數量與 cell({% math p_{i} %}) 的頂點數量線性相關。由 exercose 7.1 得到最多 {% math O(n) %} 的頂點。同理 arc 數量最多 {% math O(n) %}。

beach line 上的 arc 數最多為 Voronoi diagram 的 edge 數，又 Voronoi diagram 的 edge 最大數為 {% math 3n - 6 %}，也因此最多為 {% math O(n) %}

### 7.10 ###

Let P be a set of n points in the plane. Give an {% math O(nlogn) %} time algorithm to find two points in P that are closest together. Show that your algorithm is correct.

Algorithm:

1. 建造 Voronoi Diagram by fortune's algorithm－{% math O(nlogn) %}
2. 對於每一個 cell({% math p_{i} %}) 檢查鄰居 cell({% math p_{neighbor} %})，計算 {% math distance(p_{i}, p_{neighbor}) %} 的結果。Voronoi diagram 的 edge 數 {% math 3n - 6 %}，每一條 edge 檢查只需要 {% math O(1) %}。－{% math O(n) %}

證明：每一個點所張開的 cell 只會與相鄰的 cell 最近，否則不符合 Voronoi diagram 的定義。

### 7.14 ###

Show that the farthest point Voronoi diagram on n points in the plane has at most 2n−3 (bounded or unbounded) edges. Also give an exact bound on the maximum number of vertices in the farthest point Voronoi diagram.

* 歐拉公式：{% math v - e + f = 2 %}
* 一個頂點至少三條邊。
* farthest point Voronoi diagram 的最多有 n - 2 個頂點。
	* 對於每一個頂點而言，通過其相鄰 cell 對應的點的外接圓，其圓包含所有平面點。
	* 這種圓最多 n - 2 個。
* 從隨機增量算法中得知，插入一個點時，最多增加一個 vertex (繞行時，會刪除對於 cell 相交兩個線段之間的所有 vertex，這種 vertex 至少一個，並且增加與線段的交點 1 個)。
	{% math v(3) = 1, v(n) = v(n-1) + 1 \text{ for } n > 3 %}
* 考慮增加一個虛點，連接所有 unbounded edge  
{% math v - e + f = 2 \Rightarrow (n-2+1) - e + n = 2 \Rightarrow e = 2n - 3 %}。

----

## Chpater 9 ##

### 9.2 ###

The degree of a point in a triangulation is the number of edges incident to it. Give an example of a set of n points in the plane such that, no matter how the set is triangulated, there is always a point whose degree is n−1.

對於任意三角化的結果，總會有一個點的 degree = n - 1。

![9.2](http://i.imgur.com/JEnOhDo.png)

* 此點一定能用一條線區隔所有剩餘點。
* 剩餘 n - 1 個點，一定能滿足任兩點的連線不遮蔽其他的點。

example：n - 1 個點落在 {% math y = e^{x} %} 上，且 {% math x < p %}，另外一點在 {% math (p, q) %}

### 9.4 ###

Prove that the smallest angle of any triangulation of a convex polygon whose vertices lie on a circle is the same. This implies that any completion of the Delaunay triangulation of a set of points maximizes the minimum angle.

對於所有頂點共圓的凸多邊形，任何的三角化的最小角度都會相同。

1. 任何三角形必然是圓上三點。
2. 任何凸邊形的邊都會是一個三角形上的邊
3. 三角形的邊都是圓的弦，並且對應角度正比弦長大小。
4. 凸多邊形的最小弦長是多邊上的邊，又因一定會在三角形上，任何三角化一定包含這個最小角。

### 9.7 ###

Prove that all edges of DG(Pr) that are not in DG(Pr−1) are incident to pr. In other words, the new edges of DG(Pr) form a star as in Figure 9.8. Give a direct proof, without referring to algorithm DELAUNAYTRIANGULATION.

對於新增加的邊 {% math e %}，只會與新加入的點 {% math p_{r} %} 相連。

* 從 Voronoi diagram 等價 Delaunay triangulation，加入點的 cell，不會使其他兩個 cell 從沒相鄰變成相鄰。

* 對於 {% math \Delta(p_{i}, p_{j}, p_{k}) %} 之間加入 {% math p_{r} %}，由於 {% math C(p_{i}, p_{j}, p_{k}) %} 內部不存在任何點，{% math C'(p_{i}, p_{r}) %} (兩點當直徑)，得到 {% math C' \subseteq C %}，且 {% math C' %} 內部不存在任何點，確定 {% math \bar{p_{i} p_{r}} %} 屬於 Delaunay 上。同理 {% math \bar{p_{j} p_{r}} %}、{% math \bar{p_{k} p_{r}} %}。  

* 由於 {% math \Delta(p_{l}, p_{i}, p_{j}) %} 原本是 Delaunay 上，如果 flip {% math \bar{p_{i} p_{j}} %}，得到 {% math C(p_{i}, p_{r}, p_{l}) \subseteq C(p_{l}, p_{i}, p_{j}) %}、{% math C(p_{j}, p_{r}, p_{l}) \subseteq C(p_{l}, p_{i}, p_{j}) %}，確定 {% math \bar{p_{r} p_{l}} %} 屬於 Delaunay 上。  

* 也因此，對於增加的三角形進行檢查時，每次已經保證該三角形其中兩邊一定屬於 Delaunay 上，同時必然有 {% math p_{r} %}，flip 的邊一定會接到 {% math p_{r} %} 上。遞迴得證。

### 9.11 ###

A Euclidean minimum spanning tree (EMST) of a set P of points in the plane is a tree of minimum total edge length connecting all the points. EMST’s are interesting in applications where we want to connect sites in a planar environment by communication lines (local area networks), roads, railroads, or the like.

1. Prove that the set of edges of a Delaunay triangulation of P contains an EMST for P.
2. Use this result to give an O(nlogn) algorithm to compute an EMST for P.

對於歐幾里得距離的平面最小生成樹。

1. 證明 EMST 的 edge set 被 Delaunay triangulation 的 edge set 包含。(參考 [wiki](http://en.wikipedia.org/wiki/Euclidean_minimum_spanning_tree))
	目標： ** every edge not in a Delaunay triangulation is also not in any EMST **
	* 最小生成樹的性質：任何一個 cycle 上的最重邊將不會在最小生成樹中。
	* Delaunay triangulation： If there is a circle with two of the input points on its boundary which contains no other input points, the line between those two points is an edge of every Delaunay triangulation.

	> 對於鈍角三角形，最大邊必然不在 EMST 中，然而對於 Delaunay triangulation 性質，必須考慮他們兩點的 boundary (shared Voronoi edge) 是否存在。

	假設 p, q 之間沒有邊於 Delaunay，則對於任意通過 p, q 的圓都存在點 r 在圓內，從性質中發現 r 到 p, q 的距離一定小於 p q 之間的距離。同時在 EMST 中，p q r 三點會構成鈍角三角形，其中 p q 是最大邊，p q 之間必然沒有邊。

2. 找到 EMST 的 {% math O(nlogn) %} 算法
	Algorithm:
	1. 利用 Delaunay triangulation 找到所有邊－{% math O(nlogn) %}
	2. 最多有 3n - 6 條邊，利用 MST 中的 kruskal's algorithm－{% math O(ElogE) %}
	3. {% math E = O(3n-6) = O(n) %}，得到 {% math O(nlogn) %} 的做法。

### 9.13 ###

The Gabriel graph of a set P of points in the plane is defined as follows:  
p q Two points p and q are connected by an edge of the Gabriel graph if and only if the disc with diameter pq does not contain any other point of P.

1. Prove that DG(P) contains the Gabriel graph of P.
2. Prove that p and q are adjacent in the Gabriel graph of P if and only if the Delaunay edge between p and q intersects its dual Voronoi edge.
3. Give an O(nlogn) time algorithm to compute the Gabriel graph of a set of n points

Gabriel graph：任兩點之間為直徑的圓內若沒有其他點，則兩點之間有邊。

1. 證明 subgraph 關係。 
* 根據 Theorem 9.6 (1) 任三點圓內 {% math C(p_{i}, p_{j}, p_{k}) %}沒有其他點，但是 {% math C(p_{i}, p_{j}) %} 內部可能存有其他點 (如單純的 n = 3 的鈍角三角形)。找到 {% math e_{p_{i}, p_{j}} \notin Gabriel \text{ but } e_{p_{i}, p_{j}} \in Delaunay %} 
* {% math C(p_{i}, p_{j}) %} 內部沒有其他點，則兩點之間必然有 shared Voronoi edge，符合 Theorem 9.6 (2)。得到  
{% math \text{ if }e_{p_{i}, p_{j}} \in Gabriel \text{ , then } e_{p_{i}, p_{j}} \in Delaunay %}，得證 {% math g(P) \subseteq DG(P) %}。
2. 如果 {% math \bar{pq} %} 經過多個 Voronoi edge，則 {% math \bar{pq} %} 上一點 x 滿足 
{% math-block %}
\bar{xr} < \bar{xp}, \bar{xr} < \bar{xq} \\
\Rightarrow \angle rpx < \angle prx, \angle rqx < \angle xrq \text{(triangle)} \\
\text{let } \angle rpx = a, \angle prx = c, \angle rqx = b, \angle xrq = d \\
\Rightarrow a + b + c + d = 180^{\circ} \\ 
\Rightarrow c + d > 90^{\circ}
{% endmath-block %}

符合圓內角性質，點 r 一定在圓內，得證 {% math e_{p_{i}, p_{j}} \notin Gabriel %}。

3. 在 O(n logn) 時間內完成。
	Algorithm： 
	1. 利用 Delaunay triangulation 找到所有邊－{% math O(nlogn) %}
	2. 將 {% math e_{p{i}, p{j}} \in DG(P) %} 進行測試是否有點落在 {% math C(p{i}, p{j}) %}－{% math O(n) %}

	只需要拿鄰居進行檢測，鄰居最多 2 個 (共邊的三角形)。

### 9.14 ###

The relative neighborhood graph of a set P of points in the plane is defined as follows: Two points p and q are connected by an edge of the relative neighborhood graph if and only if

{% math-block %}
d(p, q) \leq \underset{r \in P, r \neq p, q }{min} max(d(p, r), d(q, r)).
{% endmath-block %}

1. Given two points p and q, let lune(p,q) be the moon-shaped region p q lune(p,q) formed as the intersection of the two circles around p and q whose radius is d(p,q). Prove that p and q are connected in the relative neighborhood graph if and only if lune(p,q) does not contain any point of P in its interior.
2. Prove that DG(P) contains the relative neighborhood graph of P.
3. Design an algorithm to compute the relative neighborhood graph of a given point set.

整體而言類似 9.13。

1. 若 p, q 之間沒有邊，則  
{% math-block %}
\exists r : d(p, q) > \underset{r \in P, r \neq p, q }{min} max(d(p, r), d(q, r)) \\
\exists r : d(p, q) > d(p, r) \text{ and } d(p, q) > d(q, r)
{% endmath-block %}
AND 就是做交集操作，不知道該怎麼寫才好。
2. 與 9.14 依樣畫葫蘆，只是 {% math lune(p, q) \subseteq C(p, q) %}，則更暗示 {% math \text{ if }e_{p_{i}, p_{j}} \in G \text{ , then } e_{p_{i}, p_{j}} \in Gabriel %} 
3. 速度是 {% math O(n^{2}) %}，沒辦法單純看鄰居進行檢查。拿每一條邊進行 O(n) 窮舉。不過在分散式計算，整體是 O(n)。