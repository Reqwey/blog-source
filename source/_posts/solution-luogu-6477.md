---
title: 【Solution】[NOI Online #2 提高组] 子序列问题
date: "2020-03-01 00:00:00"
tags:
 - DP
categories:
 - [OI, Solution]

---

[题目传送门。](https://www.luogu.com.cn/problem/P6477)

<!--more-->

题目要我们求解 $\Sigma{((f(l,r))^2)}$，于是我们构造一个 $g(l,r)$ 使得

$$g(l,r)=\frac{f(l,r)\times(f(l,r)+1)}{2}$$

这样，会有

$$(f(l,r))^2=2g(l,r)-f(l,r)$$

然后我们只需要分别求解 $g$ 和 $f$

定义 $last_i$ 表示与 $A_i$ **相等**的**最近**一个 $A_j$ 的下标 $j$。特别地，若没有，则为 $0$。

我们有以下性质：

$$\sum_{l=1}^{r+1}{f(l,r+1)}=\sum_{l=1}^{r}{f(l,r)}+\sum_{l=last_{r+1}+1}^{r+1}{1}$$

这是因为加入一个 $A_{r+1}$ 时，只有当 $l\in[last_{r+1}+1,r+1]$ 时，区间 $[l,r+1]$ 中才会认为 $A_{r+1}$ 一个新的值（在这之外的值会认为是重复的），会导致$f(l,r)$ 的值 $+1$。

遍历区间右端点 $r$，同时构造一棵线段树，它维护的区间 $[1,n]$ 存储 $f(1,r) \sim f(n,r)$ 的值。当 $r$ 变为 $r+1$ 时，$[last_{r+1}+1,r+1]$ 区间 $+1$。

我们又有一个性质：

令 $h(x)=\frac{x\times(x+1)}{2}$，则有

$$
\begin{aligned}
h(x+1)-h(x)&=\frac{(x+1)\times(x+2)}{2}-\frac{x\times(x+1)}{2}\\
&=x+1
\end{aligned}
$$

于是

$$\sum_{l=1}^{r+1}{g(l,r+1)}=\sum_{l=1}^{r}{g(l,r)}+\sum_{l=last_{r+1}+1}^{r+1}{f(l,r+1)}$$

这可以用线段树的区间查找操作解决。

开始时构造 $last$ 数组 $\Theta (n \log{n})$，线段树 $\Theta (n \log{n})$，总时间复杂度 $\Theta (n \log{n})$。

代码：

```cpp
#define IL inline

typedef long long ll;

const int N = 1e6 + 5;
const ll MOD = 1e9 + 7;
ll a[N];
int last[N];
std::map<ll, int> map;
int n;

IL void init() { // 输入和构造 last 数组
	std::cin >> n;
	for (int i = 1; i <= n; ++i) {
		std::cin >> a[i];
		last[i] = map[a[i]];
		map[a[i]] = i;
	}
}

ll tree[N << 2], tag[N << 2];

#define ls X << 1, L, mid
#define rs X << 1 | 1, mid + 1, R

IL void pushdown(int X, int L, int R) {
	int mid = (L + R) >> 1;
	tree[X << 1] += tag[X] * ll(mid - L + 1) % MOD, tag[X << 1] += tag[X];
	tree[X << 1 | 1] += tag[X] * ll(R - mid) % MOD, tag[X << 1 | 1] += tag[X];
	tag[X] = 0;
}

IL void pushup(int X, int L, int R) {
	tree[X] = (tree[X << 1] + tree[X << 1 | 1]) % MOD;
}

void insert(int X, int L, int R, int l, int r, ll add) {
	if (l <= L && R <= r) tree[X] += add * ll(R - L + 1) % MOD, tag[X] += add;
	else {
		pushdown(X, L, R);
		int mid = (L + R) >> 1;
		if (mid >= l) insert(ls, l, r, add);
		if (mid < r) insert(rs, l, r, add);
		pushup(X, L, R);
	}
}

ll query(int X, int L, int R, int l, int r) {
	if (l <= L && R <= r) return tree[X];
	else {
		pushdown(X, L, R);
		int mid = (L + R) >> 1;
		ll ans = 0;
		if (mid >= l) ans = (ans + query(ls, l, r)) % MOD;
		if (mid < r) ans = (ans + query(rs, l, r)) % MOD;
		return ans;
	}
}

IL void work() {
	ll sum_f = 0, g = 0, sum_g = 0;
	for (int i = 1; i <= n; ++i) {
		insert(1, 1, n, last[i] + 1, i, 1);
		sum_f = (sum_f + tree[1]) % MOD;
		g = (g + query(1, 1, n, last[i] + 1, i)) % MOD;
		sum_g = (sum_g + g) % MOD;
	}
	std::cout << (MOD - sum_f + sum_g + sum_g) % MOD;
}

int main() {
	std::ios::sync_with_stdio(false), std::cin.tie(0), std::cout.tie(0);
	
	init();
	work();
	return 0;
}
​```https://loj.ac/p/6669)
```