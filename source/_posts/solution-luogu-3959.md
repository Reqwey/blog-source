---
title: 【Solution】[NOIP2017 提高组] 宝藏-状压DP
date: "2021-07-31 00:00:00"
tags:
 - 动态规划，DP
 - 卡常
categories:
 - [OI, Solution]

---

[原题传送门](https://www.luogu.com.cn/problem/P3959)

<!--more-->

## 理论

以每个点为转移显然不行。因为贡献和之前的长度有关。

因此考虑集合之间的转移。设从节点 $j$ 出发开始挖要转移到集合 $S(j \notin S)$，他到起点的距离为 $i$ ， 设他从集合 $S_2$ 转移过来，则可写出这样的方程：

$$f(i,j,S)=\underset{j\in S_2\in S}{\min}(f(i,j,S-S_2)+(i+1)\times g(j,k)+f(i+1,k,S2-\{k\}))$$

## 实践

```cpp
typedef long long ll;

const int N = 13, INF = 0x3f3f3f3f;

memset(f, 0x3f, sizeof f);
memset(g, 0x3f, sizeof g);

IL void prework() {
	for (int i = 1; i < up; ++i) siz[i] = siz[i & (i - 1)] + 1;
                    // 为了剪枝。i & i-1 会导致 lowbit 那一位消失
	for (int i = 0; i < n; ++i) lowbit[1 << i] = i;
	for (int i = 1; i < up; ++i) lowbit[i] = lowbit[i & -i];
}

IL void work() {
	for (int i = 0; i < n; ++i) f[n - 1][i][0] = 0; // 初值处理1
	for (int i = n - 2; ~i; --i) // 倒序
		for (int j = 0; j < n; ++j) {
			f[i][j][0] = 0; // 初值处理2
			for (int S = 0; S < up; ++S) if (!((1 << j) & S) && siz[S] <= n - i - 1) {
			// 剪枝1。首先满足 j 不属于 S，然后由于距离肯定由点连成，所以siz要符合实际意义
				for (int S2 = S; S2; S2 = S & (S2 - 1)) if (f[i][j][S] > f[i][j][S & ~S2]) {
				// 剪枝2。必要时才转移
					int tmp = S2;
					for (int k = lowbit[tmp]; tmp; k = lowbit[tmp &= ~(1 << k)]) if (g[j][k] < INF) {
						cmin(f[i][j][S],
							f[i][j][S & ~S2] + f[i + 1][k][S2 & ~(1 << k)] + (i + 1) * g[j][k]);
					}
				}
			}
		}
}
```

