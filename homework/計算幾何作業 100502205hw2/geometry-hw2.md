title: "計算幾何 - HW02"
date: 2014-10-17 15:33:44
tags: [計算幾何]
categories: [學校課程, 計算幾何]
---

# Chapter 3 #

## 3.2 ## 

A rectilinear polygon is a simple polygon of which all edges are horizontal or vertical. Let P be a rectilinear polygon with n vertices. Give an example to show that {% math \left \lfloor n/4 \right \rfloor %} cameras are sometimes necessary to guard it.

-----

正交多邊形是其中一種多邊形，邊與邊之間不是平行就是垂直。至少需要 n/4 個攝影機才能監視每一個角落。

The rectilnear polygon would contain {% math \left \lfloor n/4 \right \rfloor %} parallel "alleys". At least {% math \left \lfloor n/4 \right \rfloor %} cameras are needed because no cameras can see more than one alleys.

正交多邊形，可以產生 {% math \left \lfloor n/4 \right \rfloor %} 個平行的走廊，每一個攝影機只能顧及一個走廊，因此得到一個最簡單的例子需要 {% math \left \lfloor n/4 \right \rfloor %} 個攝影機。

## 3.7 ##

Let P be a simple polygon with n vertices, which has been partitioned into monotone pieces. Prove that the sum of the number of vertices of the pieces is O(n).

-----

證明一個 n 個節點的簡單多邊形，拆成多個 monotone pieces，節點總數仍然是 O(n)。

一個簡單多邊形可以三角化成 n - 2 個三角形，每個三角形都是一個 monotone piece，因此節點個數總和 {% math 3 * (n - 2) = O(n) %}

證明一個簡單多邊形有 n 個頂點，可以切成 n - 2 個三角形。

* n = 3 時，T(3) = 1, 符合 T(n) = n - 2 成立
* 當 n = k 時，k >= 3, 且符合 T(n) = n - 2
* n = k + 1 時，T(n + 1) = T(n) + 1 = (n - 2) + 1 = n - 1，

切割的證明為，找到多邊形的最左側點，然後他一定是凸的，將相鄰兩點拉一條線，如果構成的三角形內部沒有其他點，則直接變成 n - 1 個節點的多邊形，如果裡面有點，則挑一個最靠近最左側點的那個點，將最左側那個點與其相連，這時劃分成兩個多邊形，保證算法一樣。


## 3.11 ##

Give an efficient algorithm to determine whether a polygon P with n
vertices is monotone with respect to some line, not necessarily a horizontal
or vertical one.

-----

請參考 [[ACM 題目] 單調測試](http://morris821028.github.io/2014/10/17/mproblem-monotone/) 的解法。

主要解法複雜度為 `O(n log n)`，採用角度掃描。

# Chapter 4 #

## 4.2 ##

Consider the casting problem in the plane: we are given polygon P and a 2-dimensional mold for it. Describe a linear time algorithm that decides whether P can be removed from the mold by a single translation.

-----

1. 對於 P 的任一面 {% math f_{i} %} 的法向量 {% math \vec{n_{i}} %}，找到一個移動向量 {% math \vec{d} %}，使其 {% math \vec{n_{i}} %} 和 {% math \vec{d} %} 張角全大於 90 度。也就是內積小於 0。
2. 給 {(n1x, n1y), (n2x, n2y), ...}
`nix * dx + niyy * dy <= 0`

3. 如果不單純派 `dy = 1`，調用 2DRANDOMIZEDBOUNDLP 判斷是否有解即可，不必最佳化其結果。

## 4.8 ##

The plane z = 1 can be used to represent all directions of vectors in 3-dimensional space that have a positive z-value. How can we represent all directions of vectors in 3-dimensional space that have a non-negative z-value? And how can we represent the directions of all vectors in 3-dimensional space?

-----

1. {% math z = 0 \cup z = 1 %}
2. {% math z = -1 \cup z = 1 \cup x = -1 \cup x = 1 \cup y = -1 \cup y = 1 %}，有人問說單純 {% math z = -1 \cup z = 1 %} 不就包含了所有方向嗎？但是我思考了一下收斂問題，這之間到底有沒有連續？極限上是相同，但是包不包含呢？這一點我比較擔憂，總之找一個方法將原點包起，保證原點拉到任一個面都能產生出所有方向，我附的答案是六面體，最簡單的四面體都然也可以，但是不太好寫。

## 4.16 ## 

On n parallel railway tracks n trains are going with constant speeds v1, v2, . . . , vn. At time t = 0 the trains are at positions k1, k2, . . . , kn. Give an O(nlogn) algorithm that detects all trains that at some moment in time are leading. To this end, use the algorithm for computing the intersection of half-planes.

-----

* 公式：{% math X_{i}(t) = K_{i} + V_{i} *  t%}
* 對於所有 polyhedral set {% math H = {(t, x) : \forall i; X \geq X_{i}(t)} %}

之後將這些半平面做交集，看交集結果的邊屬於哪一個半平面的邊界，哪一個火車就曾經領先過。套用半平面求交集只需要 `O(n log n)`

請參考 [[ACM 題目] 少女與戰車](http://morris821028.github.io/2014/10/12/mproblem-tanks/)