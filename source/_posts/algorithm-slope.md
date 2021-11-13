---
title: 【算法笔记】斜率优化动态规划
tags:
 - 动态规划，DP
categories:
 - [OI, Algorithm]
---

## Intro
单调队列优化 DP 需要状态变量 $j$ 与决策变量 $k$ 分离，而遇到交叉项如 $i\times j$ 时则无能为力。因此我们需要新的方法。

<!--more-->

## Example

**【例题】玩具装箱**

[题目传送门。](https://www.luogu.com.cn/problem/P3195)

$$
\begin{align}
S_i&=\sum_{j\leq i}{C_i}+i\\
f_{i, j}&=\min(f_{i - 1, k} + (S_j - S_k - L - 1)^2)\\
f_{i, j}&=f_{i - 1,k} + (S_j - S_k - L-1)^2\\
f_j&=f_k+S_j^2-2S_j(S_k+L+1)+(S_k+L+1)^2\\
f_k+(S_k+L+1)^2&=2S_j\cdot S_k+(f_j+2(L+1)S_j-S_j^2)
\end{align}
$$

这里为了打消平方项的影响，我们直接把 $(S_k+L+1)^2$ 设置成了 $y$ 轴。

![slope-1](/images/slope-1.png)

**Code**

```cpp Init
IL void init() {
	rd<int>(n), rd<ll>(L);
	for (int i = 1; i <= n; c[i] += c[i - 1] + 1, ++i) rd<ll>(c[i]);
	for (int i = 1; i <= n; ++i) Debug("%d\n", c[i]);
}
```

```cpp DP
IL Lf slope(int u, int v) {
	Lf x_u = X(u), x_v = X(v), y_u = Y(u), y_v = Y(v);
	if (x_u == y_u) return 91288193999999.392699;
	return (y_v - y_u) / (x_v - x_u);
}

IL void work() {
	int l = 1, r = 0;
	q[++r] = 0;
	for (int j = 1; j <= n; ++j) {
		while (l < r && slope(q[l], q[l + 1]) <= c[j] * 2.0)
			++l;
		f[j] = f[q[l]] + (c[j] - b(q[l])) * (c[j] - b(q[l]));
		Debug("dp[%d] is %d while using %d\n", j, f[j], l);
		while (l < r && slope(q[r - 1], q[r]) >= slope(q[r - 1], j))
			--r;
		q[++r] = j;
	}
	printf("%lld\n", f[n]);
}
```