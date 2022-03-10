---
title: 【Solution】Almost Sorted-状压DP
date: "2022-03-09 00:00:00"
tags:
 - 动态规划，DP
categories:
 - [OI, Solution]

---

[题目传送门。](https://atcoder.jp/contests/arc132/tasks/arc132_c)

<!--more-->

## 诊断分析

我们发现 $n$ 比较大，直接 $2^n$ 暴力肯定不行，想着 $d$ 很小，能不能有什么性质~~和判定~~。

我们发现，位置 $i$ 只能选 $[i-d,i+d]$ ，意味着状态只有 $2d+1$ 种。

## 能力测评

假设 $1 \sim i-1$ 构成状压集合 $\texttt{S}$，表示 $[(i-1)-d,(i-1)+d]$ 内的选数情况。

我们要保证一件事，就是 $\texttt{S & 1 = 1}$  。因为 $i$ 够不着 $i-d-1$。

然后取 $\texttt{S' = S >> 1}$ 表示  $[i-d,i+d]$ 内的选数情况。

在进行内层循环 $j\in [0,d+d]$。必须满足 $j \notin S$。

分两种情况讨论：

- $a_i\neq -1$：如果 $a_i=i+(d-j)$ 那么就直接把 $f(i,S'\cup\{a_i\})\leftarrow f(i,S'\cup\{a_i\})+f(i-1,S)$。
- $a_i=-1$：同样 $f(i,S'\cup\{i+(d-j)\})\leftarrow f(i,S'\cup\{i+(d-j)\})+f(i-1,S)$。

## 课堂小结

```cpp
const int N = 504, D = (1 << (5 + 5 + 1)) + 4;
const ll MOD = 998244353;

int a[N];
ll f[N][D];
int n, d;

IL void madd(ll &a, ll b) { a = (a + b) % MOD; }
IL void cmax(ll &a, ll b) { a < b ? (a = b) : 0; }

int main() {
	std::ios::sync_with_stdio(false), std::cin.tie(0), std::cout.tie(0);
	std::cin >> n >> d;
	for (int i = 1; i <= n; ++i)
		std::cin >> a[i], --a[i]; // 方便状压
	f[0][(1 << (d + 1)) - 1] = 1; // 代表比他小的都选完了，注意不是 0
	for (int i = 1; i <= n; ++i) {
		for (int set = 1; set < 1 << (d + d + 1); set += 2) {
		// 从 1 开始，+= 2 确保最左一位始终为 1，是合法的
			int newset = set >> 1;
			for (int j = 0; j <= d + d; ++j) {
				if (a[i] >= 0 && a[i] != i - 1 + j - d) continue;
				else if (~newset & (1 << j)) madd(f[i][newset | (1 << j)], f[i - 1][set]);
			}
		}
	}
	std::cout << f[n][(1 << (d + 1)) - 1]; // 代表比他小的都选完了，注意不是 0
	return 0;
}
```

