---
title: 【Solution】AtCoder Edu DP Round 两道
date: "2022-03-10 00:00:00"
tags:
 - DP
categories:
 - [OI, Solution]

---

[比赛传送门。](https://atcoder.jp/contests/dp)

<!--more-->

## V. Subtree-换根 DP

[题目传送门。](https://atcoder.jp/contests/dp/tasks/dp_v)

### Down

$$\text{down}(v)=\prod \limits_{u\ v连边} (\text{down}(u)+1)$$

### Up

由于模数不一定质数，不能用除法，因此正着乘一遍，反着乘一遍就好。

```cpp
void up(int u, int rt) {
	ll tmp = f[u][1];
	for (ui i = 0; i < g[u].size(); ++i) if (g[u][i] != rt) {
		f[g[u][i]][1] *= tmp %= m;
		(tmp *= (f[g[u][i]][0] + 1)) %= m;
	}
	tmp = 1;
	for (ui i = g[u].size() - 1; ~i; --i) if (g[u][i] != rt) {
		f[g[u][i]][1] *= tmp %= m;
		(tmp *= (f[g[u][i]][0] + 1)) %= m;
	}
	for (auto &v : g[u]) if (v != rt) {
		++f[v][1]; up(v, u);
	} 
}
```

## W. Intervals-线段树优化 DP

[题目传送门。](https://atcoder.jp/contests/dp/tasks/dp_w)

按右端点从小到大排序，将 $[1,i-1]$ 状态集合里面取个最大值加到该点对应的线段树中，然后把右端点 $=i$ 的区间加入线段树中，表示他能对之前的一些状态产生影响。

```cpp
int n, m;

struct req {
	int l, r; ll w;
} a[N];

ll tree[N << 2], tag[N << 2];

IL void cmax(ll &a, ll b) { a < b ? (a = b) : 0; }

IL void pushdown(int x) {
	if (tag[x]) {
		tree[x << 1] += tag[x], tag[x << 1] += tag[x];
		tree[x << 1 | 1] += tag[x], tag[x << 1 | 1] += tag[x];
		tag[x] = 0;
	}
}

void insert(int x, int l, int r, int L, int R, ll w) {
	if (L <= l && r <= R) { tree[x] += w, tag[x] += w; }
	else {
		pushdown(x);
		int mid = (l + r) >> 1;
		if (L <= mid) insert(x << 1, l, mid, L, R, w);
		if (R > mid) insert(x << 1 | 1, mid + 1, r, L, R, w);
		tree[x] = std::max(tree[x << 1], tree[x << 1 | 1]);
	}
}

ll query(int x, int l, int r, int L, int R) {
	if (L <= l && r <= R) return tree[x];
	else {
		pushdown(x);
		int mid = (l + r) >> 1;
		ll ans = -INT64_MAX;
		if (L <= mid) cmax(ans, query(x << 1, l, mid, L, R));
		if (R > mid) cmax(ans, query(x << 1 | 1, mid + 1, r, L, R));
		return ans;
	}
}

int main() {
	std::ios::sync_with_stdio(false), std::cin.tie(0), std::cout.tie(0);
	std::cin >> n >> m;
	for (int i = 1; i <= m; ++i)
		std::cin >> a[i].l >> a[i].r >> a[i].w;
	std::sort(a + 1, a + m + 1, [](req x, req y) { return x.r < y.r; });
	for (int i = 1, j = 1; i <= n; ++i) {
		insert(1, 1, n, i, i, query(1, 1, n, 1, i));
		while (a[j].r == i && j <= m) insert(1, 1, n, a[j].l, a[j].r, a[j].w), ++j;
	}
	std::cout << std::max(0LL, query(1, 1, n, 1, n));
	return 0;
}
```

