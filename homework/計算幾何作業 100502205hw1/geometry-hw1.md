title: "計算幾何 - HW01"
date: 2014-10-05 15:26:10
tags:
categories: [學校課程, 計算幾何]
---

# Chapter 1 #

## 1.1 ## 

The convex hull of a set S is defined to be the intersection of all convex sets that contain S. For the convex hull of a set of points it was indicated that the convex hull is the convex set with smallest perimeter. We want to show that these are equivalent definitions

a. Prove that the intersection of two convex sets is again convex. This implies that the intersection of a finite family of convex sets is convex as well.  
b. Prove that the smallest perimeter polygon P containing a set of points P is convex.
c. Prove that any convex set containing the set of points P contains the smallest perimeter polygon P.

-----

a. 證明兩個凸包交集仍然是凸包
假設凸包分別為 {% math C1, C2 %}，題目所求 {% math C = C1 \bigcap C2 %}
已知：A set {% math K %} is convex if each {% math u \neq v %}, the line-segment {% math \bar{uv} %} is contained in {% math K %}, {% math \bar{uv}\subseteq K %}
假設 {% math C %} 不是凸包，則存在 {% math \bar{uv} \nsubseteq C %}，根據定義 {% math u, v \in C1, C2 %}，得到 {% math \bar{uv} \nsubseteq C1, C2 %}，矛盾得證 {% math C %} 一定是凸包。

b. 證明最小周長的多邊形 P 包含所有點集 S 一定是凸包
假設 {% math P%} 是最小周長的非凸包多邊形，令 {% math x, y \in P, and \bar{xy} \nsubseteq P%}，則 {% math \bar{xy} %} 會交 {% math P %} 於至少兩點 {% math x', y' %}，{% math P' %} 是將 {% math \bar{x^{'}y^{'}} %} 連起所產生的新多邊形，顯然地 {% math P' %} 的周長更小。矛盾得證。

c. 證明任何一個凸多邊形 C 包含點集 S 的同時也一定會包含最小周長的多邊形 P。
假設有 vertex {% math v \in P, but v \notin C %}，同時 v 不會在 S 範圍中，因為 C 已經包含了 S。{% math v1, v2 %} 為 {% math C, P%} 交點，則 {% math P' %} 是 {% math v1, v2%} 相連產生的多邊形，則 P' 藉由 (b) 一定是多邊形，v1 到 v2 的距離更短，找到一個周長更小的多邊形。矛盾得證。

## 1.3 ##

Let E be an unsorted set of n segments that are the edges of a convex polygon. Describe an O(nlogn) algorithm that computes from E a list containing all vertices of the polygon, sorted in clockwise order.

-----

Algorithm:
1. 得到所有點 {% math {(x1, y1), (x2, y2), ..., (xn, yn)} %}，並且附加是屬於哪兩個邊的端點對點作排序。`map< point, vector<seg> > R` - `O(n log n)`
2. 挑最左下的角當作 {% math (x1, y1) %} 的其中一邊
{% codeblock %}
A[0] = (x0, y0) = R.begin().first;
for (int i = 0; i < E.size(); i++) {
	for (seg s : R[A[0]]) {
		if (s.p0 == A[i] && (i == 0 || s.p1 != A[i-1]))
			A[i+1] = s.p1;
	}
}
{% endcodeblock %}
3. 檢查是否為順時針，否則反轉序列 - `O(n)`

## 1.8 ##

The O(nlogn) algorithm to compute the convex hull of a set of n points in the plane that was described in this chapter is based on the paradigm of incremental construction: add the points one by one, and update the convex hull after each addition. In this exercise we shall develop an algorithm based on another paradigm, namely divide-and-conquer.
	
a. Let P1 and P2 be two disjoint convex polygons with n vertices in total. Give an O(n) time algorithm that computes the convex hull of P1 ∪P2.
b. Use the algorithm from part a to develop an O(nlogn) time divide-andconquer algorithm to compute the convex hull of a set of n points in the plane.

-----

D&C 的 `O(n log n)` 凸包算法

a. 將兩個沒有相交的凸包，用 `O(n)` 時間內合併凸包 {% math P1 \cup P2 %}。

假設兩個凸包儲存方式都是逆時針順序，並第一個節點為最左下的節點。
Algorithm:

1. 將最左側的凸包另為 P1，反之 P2。
2. 代碼如下，找到下凸包 - `O(n)`
{% codeblock %}
vector C
C[m = 0] = P1[0]
for (i = 1, j = 0; i < P1.size(); i++) {
	if (m >= 2 && cross(C[m-1] - C[m-2], P2[j] - C[m-2]) <= 0)
		break;
	C[m++] = P1[i]
}
for (; j < P2.size(); j++)
	while (m >= 2 && cross(C[m-1] - C[m-2], P2[j] - C[m-2]) <= 0)
		m--;
	C[m++] = P2[j];
{% endcodeblock %}
3. 仿 __2.__ 反過來作，找到上凸包 - `O(n)`
4. 上下凸包合併 - `O(n)`

b. 
Algorithm:
1. 將所有點按照 x 做升排序 - `O(n log n)`
2. 
{% codeblock %}
convex DC(int l, int r, point p[]) {
	if (l == r)
		return convex(p[l])
	convex leftconvex = DC(l, (l + r)/2, p);
	convex rightconvex = DC((l+r)/2 + 1, r, p);
	return merge(leftconvex, rightconvex);
}
{% endcodeblock %}

Prove {% math T(n) = 2 T(n/2) + O(n)%}, 
by master theorem: {% math a = 2, b = 2, c = 1, log_{a}b = c, \Rightarrow T(n) = \theta (n^{c} log n) = \theta (n log n)%}

-----

# Chapter 2 #

## 2.1 ## 

Let S be a set of n disjoint line segments whose upper endpoints lie on the line y=1 and whose lower endpoints lie on the line y=0. These segments partition the horizontal strip [−∞ : ∞]×[0 : 1] into n+1 regions. Give an O(nlogn) time algorithm to build a binary search tree on the segments in S such that the region containing a query point can be determined in O(logn) time. Also describe the query algorithm in detail.

-----

[參閱實作 b321: 河道分界](http://zerojudge.tw/ShowProblem?problemid=b321)

Algorithm: (build)
1. sort 線段 (根據上方的 (x, 1) 進行由小排到大 ) - `O(n log n)`
2. 靜態建造，動態建造請參閱可平衡的 BST。因為任切一條 y = k 的線，保證相交的 x 值得順序不會變 (跟排序結果的順序相比)，因此一開始挑 y = 1 來做排序依據。
{% codeblock %}
root = dfs(0, n - 1, segments) - `O(n)`
node dfs(int l, int r, segment segs[]) {
	if (l == r)
		return new node(segs[l]);
	else if (l < r)
		int mid = (l + r)/2;
		node ret = new node(segs[mid]);
		ret.lson = dfs(l, mid - 1, segs);
		ret.rson = dfs(mid + 1, r, segs);
		return ret;
	else
		return NULL;
}
{% endcodeblock %}

Algorithm: (query)
1. 令 lbound = null, rbound = null
2. 走訪 BST
{% codeblock %}
void query(node u, point p) {
	if u == NULL
		return;
	if p leftside by u.seg
		rbound = u
		dfs(u.lson, p)
	else
		lbound = u
		dfs(u.rson, p)
}
{% endcodeblock %}
3. output `(lbound, rbound)`

## 2.5 ## 

Which of the following equalities are always true?
{% math-block %}
(1) Twin(Twin(\vec{e})) = \vec{e} \\
(2) Next(Prev(\vec{e})) = \vec{e} \\
(3) Twin(Prev(Twin(\vec{e}))) = Next(\vec{e}) \\
(4) IncidentFace(\vec{e}) = IncidentFace(Next(\vec{e})) \\
{% endmath-block %}

-----

(1)(2)(4) are always true. (3) may not be true.

## 2.6 ## 

Give an example of a doubly-connected edge list where for an edge e the faces {% math IncidentFace(\vec{e}) %} and {% math IncidentFace(Twin(\vec{e})) %} are the same.

-----

已知 {% math IncidentFace(\vec{e}) = IncidentFace(Next(\vec{e})) %} always true，讓 {% math Next(\vec{e}) = Twin(\vec{e}) %} always true。

| Half-edge | Orign | Twin | IncidentFace | Next | Prev |
|-----------|-------|------|--------------|------|------|
| {% math \vec{e_{1, 2}} %} | {% math v_{1} %} | {% math \vec{e_{2, 1}} %} | f1 | {% math \vec{e_{2, 1}} %} | {% math \vec{e_{2, 1}} %} |
| {% math \vec{e_{2, 1}} %} | {% math v_{2} %} | {% math \vec{e_{1, 2}} %} | f1 | {% math \vec{e_{1, 2}} %} | {% math \vec{e_{1, 2}} %} |

## 2.14 ##

Let S be a set of n disjoint line segments in the plane, and let p be a point not on any of the line segments of S. We wish to determine all line segments of S that p can see, that is, all line segments of S that contain some point q so that the open segment pq doesn’t intersect any p not visible line segment of S. Give an O(nlogn) time algorithm for this problem that uses a rotating half-line with its endpoint at p.

----

[參閱實作 b325: 人格分裂](http://zerojudge.tw/ShowProblem?problemid=b325)

Algorithm:
1. 對於所有的端點相對 p 做極角排序，並且知道相對應的角度上會存有那些線段的端點。
{% codeblock %}
map<double, vector<int, int> > angle;
for (int i = 0; i < n; i++) {
	double v1 = atan2(D[i].s.y - pos.y, D[i].s.x - pos.x);
	double v2 = atan2(D[i].e.y - pos.y, D[i].e.x - pos.x);
	angle[v1].push_back(make_pair(i, 0));
	angle[v2].push_back(make_pair(i, 1));
}
{% endcodeblock %}
2. 藉由出現的角度，使用極角掃描，一開始必須將碰的線段加入。
{% codeblock %}
double c;
CMP::base = pos;
double ftheta = angle.begin()->first;
pair<int, int> u = angle.begin()->second[0];
Pt fpt;
if (u.second == 0)
	fpt = D[u.first].s;
else
	fpt = D[u.first].e;
for (int i = 0; i < n; i++) {
	if (cross(pos, fpt, D[i].s) * cross(pos, fpt, D[i].e) < 0)
		S.insert(D[i]);
}
CMP::sin = sin(ftheta);
CMP::cos = cos(ftheta);
for (map<double, vector< pair<int, int> >, CMP2>::iterator it = angle.begin();
	it != angle.end(); it++) {
	CMP::sin = sin(it->first);
	CMP::cos = cos(it->first);
	for (int i = 0; i < it->second.size(); i++) {
		pair<int, int> u = it->second[i];
		if (u.second == 0)
			c = cross(pos, D[u.first].s, D[u.first].e);
		else
			c = cross(pos, D[u.first].e, D[u.first].s);
		if (fabs(c) > eps) {
			if (c > 0)
				S.insert(D[u.first]);
			else
				S.erase(D[u.first]);
		}
	}
	if (S.size()) {
		visual[S.begin()->label] = 1;
	}
} 
{% endcodeblock %}

# 心得 #

出題目，出題目，萌萌哒