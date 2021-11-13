---
title: 【Solution】Muddy Fields G-二分图匹配
date: "2021-10-18 21:28:00"
tags:
 - 二分图
 - 匈牙利算法
categories:
 - [OI, Solution]
---

[题目传送门。](https://www.luogu.com.cn/problem/P6062)

## 建模

> 二分图匹配的模型有两个要素：
>
> 1. 节点能分成独立的两个集合，每个集合内部有 $0$ 条边：**$0$ 要素**
>
> 2. 每个节点只能与 $1$ 条匹配边相连：**$1$ 要素**
>
>    ——摘自李煜东《算法竞赛进阶指南》

<!--more-->

分析一下这道题的两个要素：

1. 每条**横向木板**之间互不交叉，每条**纵向木板**之间互不交叉：**$0$ 要素**
2. 每个**泥地**仅能被 $1$ 条**最优横向木板**和 $1$ 条**最优纵向木板**包含：**$1$ 要素**

那我们可以以每个**泥地**为边，以包含其的**最优横向木板**和**最优纵向木板**为其左右端点，建立二分图。

题目要求**最少木板数**覆盖全部的**泥地**，这可以转化成**一次操作可以选择一个点，将所连的边染色，目的是让所有边被染色**，然后求最少需要的点数，即为二分图**最小点覆盖**。

而**最小点覆盖**等于**最大匹配**，[二分图最大匹配的König定理及其证明 | Matrix67: The Aha Moments](http://www.matrix67.com/blog/archives/116)，故建图跑匈牙利即可。

## 匈牙利算法

每次找到一条**未匹配**-**匹配**-**未匹配**-$\cdots$-**未匹配**的路径，将其取反，得到总匹配数 $+1$。

这条路径被称为**增广路**。

好了相信你（我）已经学会匈牙利算法了。

## 代码

```cpp
#include <bits/stdc++.h>

const int N = 55;

bool map[N][N], g[N * N][N * N], vis[N * N];
int r[N][N], c[N][N], match[N * N];

int crow, ccol;

bool dfs(int x) {
	for (int y = 1; y <= ccol; ++y)
		if (g[x][y] && !vis[y]) {
			vis[y] = true;
			if (!match[y] || dfs(match[y])) return match[y] = x, true;
		}
	return false;
}

int main() {
#ifdef ONLINE_JUDGE
#endif
#ifdef LOCAL
	clock_t c1 = clock();
	freopen("in", "r", stdin);
	freopen("out", "w", stdout);
#endif
	int n, m;
	char cc;
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n; ++i)
		for (int j = 1; j <= m; ++j) {
			std::cin >> cc;
			if (cc == '*') {
				map[i][j] = true;
				if (j == 1 || !map[i][j - 1]) ++crow;
				r[i][j] = crow;
			}
		}
	for (int j = 1; j <= m; ++j) {
		for (int i = 1; i <= n; ++i)
			if (map[i][j]) {
				if (i == 1 || !map[i - 1][j]) ++ccol;
				c[i][j] = ccol;
				g[r[i][j]][c[i][j]] = true;
			}
	}
	int ans = 0;
	for (int i = 1; i <= crow; ++i) {
		memset(vis, 0, sizeof vis);
		ans += int(dfs(i));
	}
	printf("%d", ans);
#ifdef LOCAL
	// 当运行暴力对拍时，请注释掉下面这行
	printf("\nTime used: %ldms.\n", clock() - c1);
#endif
	return 0;
}
```

