---
title: 【Solution】 [CEOI2016]kangaroo-DP
date: "2022-03-04 00:00:00"
tags:
 - DP
categories:
 - [OI, Solution]

---

[原题传送门](https://www.luogu.com.cn/problem/P5999)

<!--more-->

即要构造这样的数列，满足 $a_{i-1} < a_i > a_{i+1}$ 即 `^` 或 $a_{i-1} > a_i < a_{i+1}$ 即 `v`。

我们考虑按从小到大的顺序插入。设此时已经做好了一些段。由于新插入的数总比之前的任何数大，因此会出现一个`^`。为了保证布局合法，我们必须要保证之前的段都是以 `/...\` 的形式。 

具体来说，设 $f(i,j)$ 为已经做到第 $i$ 位，分了 $j$ 段的方案数。

分类讨论：

- $i\neq s, i\neq t$ ：
  + 连接两个段：$f(i-1,j+1)\times j$
  + 新开一段：$f(i-1,j)\times c$。其中，若 $i>s$ 则不能开在头，$i>t$ 则不能开在尾。因此 $c=j+1-[i>s]-[i>t]$
- $i=s\ \texttt{or}\ i=t$ ：
  + 接在头、尾：$f(i-1,j)$
  + 开在头、尾：$f(i-1,j-1)$

注：当$i\neq s, i\neq t$ 时我们只存在连接**两个段**和新开这两种操作，而没有只贴在一边。因为如果贴在原来的一段的边界上就会出现上扬，即 `\...` 或 `.../` 这种情况，与题设条件矛盾。

```cpp
IL void work() {
	f[0][0] = 1;
	for (int i = 1; i <= n; ++i)
		for (int j = 1; j <= n; ++j)
			if (i != s && i != t) {
				f[i][j] = (f[i - 1][j + 1] * j % MOD + f[i - 1][j - 1] * (j - (i > s) - (i > t)) % MOD) % MOD;
			} else {
				f[i][j] = (f[i - 1][j] + f[i - 1][j - 1]) % MOD;
			}
	std::cout << f[n][1];
}
```

