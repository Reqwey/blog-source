---
title: 【题解】小凸玩矩阵-二分图+二分答案
tags:
 - 二分图
 - 匈牙利算法
 - 二分答案
categories:
 - [OI, Solution]
---

[原题传送门](https://www.luogu.com.cn/problem/P4251)

<!--more-->

## Analysis

常用技巧，把一行或一列转换为点，然后原先的点转换成这个二分图的边。

然后考虑选出第 $k$ 大的数的最小值，这个比较难以处理。那我们考虑二分这个最小值，并判断是否合理。

**怎么判断呢？**

我一开始是将 $val \geq mid$ 的边加入来跑二分图的最大匹配。但这个方法有一个严重的问题。就是这个边权最小值 $(mid)$ --最大匹配数 $(cnt)$ 的函数关系并非单增。有可能有多个边权最小值都是这种最大匹配数（函数是离散的），而如果采用这种二分方式则会找到 $cnt$ 与 $cnt+1$ 分界线上的那个 $mid$ ，这并不是我们想要的。我们想要 $cnt-1$ 与 $cnt$ 分界线上的那个才是答案。

因此我们需要转换一下思路，将 $val \leq mid$ 的边加入，将 $cnt$ 与 $n-k+1$ 进行比较。

## Code

```cpp 读入与建图
const int N = 260;

ll g[N][N], limit;
int vis[N], id, match[N];

int n, m, k;

IL void init() {
  fr<int>(n), fr<int>(m), fr<int>(k);
  for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j)
      fr<ll>(g[i][j]), limit = cmax<ll>(limit, g[i][j]);
}
```

```cpp 二分答案
IL ll binary() {
  ll l = 1, r = limit, mid;
  while (l < r) {
    mid = (l + r) >> 1;
    if (check(mid) >= n - k + 1)
      r = mid;
    else
      l = mid + 1;
  }
  return l;
}
```

```cpp Check
IL int check(ll st) {
  memclear(match, 0), memclear(vis, 0);
  int cnt = 0;
  id = 1;	// 避免重复清空 vis 数组
  for (int i = 1; i <= n; ++i, ++id)
    cnt += dfs(i, st);
  return cnt;
}
```

```cpp 匈牙利
IL bool dfs(int x, ll st) {
  for (int y = 1; y <= m; ++y)
    if (g[x][y] <= st && vis[y] != id) {
				// 避免重复清空 vis 数组
      vis[y] = id;
      if (!match[y] || dfs(match[y], st))
        return match[y] = x, true;
    }
  return false;
}
```