---
title: 【Solution】砝码称重-DP+bitset
date: "2021-07-31 00:00:00"
tags:
 - DP
 - bitset
 - 卡常
categories:
 - [OI, Solution]
---

[原题传送门](https://www.luogu.com.cn/problem/P1441)

<!--more-->

## 思路

题目要求从 $n$ 个砝码里面去掉 $m$ 个，那很自然的朴素想法是一个`DFS`跑过去，然后筛选出一种合法的方案，再套一个`01背包`统计一下答案即可

然后分析一下复杂度，达到了

$$T(n) = 2^n  \times (n \sum{a_i} + 3n) \rightarrow \Theta(2^n \cdot n \sum{a_i})$$

其中第一项是枚举子集的复杂度，之后是`01背包方案数` + `扫一遍` + `清零` + `求出背包容量t`的复杂度。

> 这里参考了 [@皎月半洒花 的题解](https://www.luogu.com.cn/blog/_post/75808)

然后这会 $\texttt{T}$ 掉。

想着怎么优化。

## 优化 DFS

把前面提到的筛选合法的方案看成一个`01串`，去掉的用 `0`，留下来的用 `1`，然后直接 `for` 循环枚举，这样可以卡掉 `DFS` 递归的常数。

这里的循环从 $2^{n - m - 1}$ 开始（因为小于 $n - m$ 个位数根本没有 $n - m$ 个 $1$），到$2^n - 1$结束。

然后判断合法性要用到 `popcount()` 函数，即统计一个数二进制表示下 `1` 的个数。

这个函数是 `gcc` 的内置函数，函数名为 `__builtin_popcount()`。~~但CCF禁止使用下划线开头的函数，所以~~我们自己编一个。

这里我看到了一个效率很高的版本，是在 [@pantw 的题解](https://www.luogu.com.cn/blog/_post/23529) 中展示的。他不用一个一个枚举位数，而是通过 `分块` + `打表` 的方式，每 $4$ 位为一组进行统计。

```cpp
const int tb[16] = {0, 1, 1, 2, 1, 2, 2, 3, 1, 2, 2, 3, 2, 3, 3, 4};

IL int popcnt(ll x) {
	int cnt = 0;
	for (; x; x >>= 4) cnt += tb[x & 15];
	return cnt;
}
```

其中 `tb` 数组存的是 $0 \sim 15$ 二进制位上 $1$ 的个数。

## 优化 01背包

嗯，先说说正常的 `01背包` 该怎么写。

> 这里参考了 [@hsfzLZH1 的题解](https://www.luogu.com.cn/blog/_post/6819)。

```cpp
memset(f, 0, sizeof f);
f[0] = true;
ans = 0;
tot = 0;					 // 清零，因为可能要调用多次
for (int i = 0; i < n; i++)	 // 从前到后选取所有的砝码
{
	if (tf[i]) continue;  // 如果被标记为已经舍弃就跳过
	for (int j = tot; j >= 0; j--)
		if (f[j] && !f[j + a[i]])
			f[j + a[i]] = true, ans++;	// 否则dp并且维护ans的值
	tot += a[i];  // 这个tot意为当前f[i]为真值的最大的i，极大加快了dp过程
}
ret = max(ans, ret);  // 更新最后的答案
```

然后咱们想，能不能也用 `01串` 表示 `DP状态` 呢？

于是咱们产生了想法。。。

> 这里参考了 [@皎月半洒花 的题解](https://www.luogu.com.cn/blog/_post/75808)

```cpp
bitset<2021> dp;

dp.reset(), dp[0] = 1, t = 0 ; 
for (j = 0 ; j < N ; ++ j) t += (1 << j & i) ? base[j + 1] : 0 ;
for (k = 0 ; k < N ; ++ k) 
    for (j = t ; j >= base[k + 1] ; -- j)
        dp[j] = (1 << k & i) ? dp[j] : (dp[j] | dp[j - base[k + 1]]) ; 
Ans = max(Ans, (int)dp.count() - 1) ;
```

哦，对了， 这个最终的 $-1$ 是把称出了 $0$ 的重量扣掉。

> 还不够？
>
> 确实。

你看这个 `DP` 是怎么转移的。由于 `dp` 是一个 `bitset`，在里层的 `for` 循环以后，从动态上看，这个`bitset` 所有的 `1` 都被移动了 `base[k + 1]` 位（注：这位老哥从 `1` 开始读入）。

因此，我们实际上是可以用**位运算**压掉这一维！！！

我们写出了如下代码：

> 这里参考了 [@pantw 的题解](https://www.luogu.com.cn/blog/_post/23529)

```cpp
bitset<2021> S(1);
for (int j = 0; j < n; j++)
    if (i & (1 << j)) S |= S << a[j];
int siz = S.count();
ans = max(ans, siz);
```

于是我们愉快的通过了这道题。。。

## 完整代码

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef pair<int, int> pii;
typedef long long ll;

#define IL inline

template <class I>
IL void read(I &x) {
	int fl = 1;
	x = 0;
	char c = getchar();
	while (!isdigit(c)) {
		if (c == '-') fl = -1;
		c = getchar();
	}
	while (isdigit(c)) x = x * 10 + c - '0', c = getchar();
	x *= fl;
}

int a[100];

const int tb[16] = {0, 1, 1, 2, 1, 2, 2, 3, 1, 2, 2, 3, 2, 3, 3, 4};

IL int popcnt(ll x) {
	int cnt = 0;
	for (; x; x >>= 4) cnt += tb[x & 15];
	return cnt;
}

int main() {
#ifdef LOCAL
	clock_t c1 = clock();
	freopen("in.in", "r", stdin);
	freopen("out.out", "w", stdout);
#endif
#ifdef ONLINE_JUDGE
#endif
	int n, m, d, ans = 0;
	read(n), read(m), d = n - m;
	for (int i = 0; i < n; i++) read(a[i]);
	for (int i = 1 << (d - 1), ln = 1 << n; i < ln; i++)
		if (popcnt(i) == d) {
			bitset<2021> S(1);
			for (int j = 0; j < n; j++)
				if (i & (1 << j)) S |= S << a[j];
			int siz = S.count();
			ans = max(ans, siz);
		}
	printf("%d\n", ans - 1);
#ifdef LOCAL
	cout << "\nTime used: " << clock() - c1 << "ms" << endl;
#endif
	return 0;
}
```