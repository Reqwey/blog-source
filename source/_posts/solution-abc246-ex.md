---
title: 【Solution】ABC 246 Ex. 01? Queries
date: "2022-04-07 00:00:00"
tags:
 - 数据结构
 - 线段树
 - 动态规划，DP
 - 矩阵快速幂
categories:
 - [OI, Solution]
---

[题目传送门 (Translated by me.)](http://www.cspoi.net/problem/4010)

<!--more-->

令 $f_{i,0/1}$ 表示以字符串前 $i$ 个字符构成的所有不同子序列中以 $0/1$ 结尾的子序列的个数。

对于当前字符 $s_i=$ `0` $\texttt{or}$ `1`  $\texttt{or}$ `?`，有不同的转移方式，以下先说明 $s_i=$ `0` 的情况：

- 首先令 $f_{i,0} \leftarrow f_{i-1,0},\ f_{i,1} \leftarrow f_{i-1,1}$。即不考虑 $s_i$ 作为结尾，直接继承上一个集合。
- 考虑由 $s_i$ 结尾的子序列有多少相对于 $f_{i-1,0}$ 的集合是没有出现过的。
- 考虑容斥，先求出结尾接上 $s_i$ 可以成为满足条件的子序列总共有 $f_{i-1,0}+f_{i-1,1}+1$ 个（$+1$ 表示空串）。
- 然后考虑接上 $s_i$ 后还是在集合 $f_{i-1,0}$ 中的子序列的个数。
- 由于在 $s_{1\sim (i-1)}$ 中 $0$ 是连续分布的，所以除了从 $s_1$ 到最后一个 $0$ 的位置所构成的**最长子序列**以外，集合 $f_{i-1,0}$ 中的其他的子序列接上一个 $0$ 都可以表示为该集合中的元素。
- 但是还要发现，单独的 `0` 也被多算了一遍，因此还要计算上。
- 因此最终答案为 $f_{i-1,0}+[(f_{i-1,0}+f_{i-1,1}+1)-(f_{i-1,0}-1+1)]=f_{i-1,0}+f_{i-1,1}+1$
- 因此，我们一共可以写出：
- $f_{i,0}\leftarrow f_{i-1,0}+f_{i-1,1}+1$​
- $f_{i,1}\leftarrow f_{i-1,1}$

同样，当  $s_i=$ `1` 时，

$$f_{i,0}\leftarrow f_{i-1,0}$$

$$f_{i,1}\leftarrow f_{i-1,0}+f_{i-1,1}+1$$

当 $s_i=$ `?` 时，

$$f_{i,0}\leftarrow f_{i-1,0}+f_{i-1,1}+1$$

$$f_{i,1}\leftarrow f_{i-1,0}+f_{i-1,1}+1$$

然后去看 [Editorial - AtCoder Beginner Contest 246](https://atcoder.jp/contests/abc246/editorial/3715)。

## Code

```cpp
/*
思路：


*/

#include <iostream>
#include <algorithm>
#include <cstring>

#define debug(...) fprintf(stderr, __VA_ARGS__)
#define Debug(...)                                                  \
  fprintf(stderr, "Line %d, func %s() : ", __LINE__, __FUNCTION__), \
      fprintf(stderr, __VA_ARGS__)
      
typedef long long ll;

#define IL inline

using std::cerr;
using std::cin;
using std::cout;
using std::endl;
using std::ios;
using std::string;

namespace IO {

template <typename T>
IL void read(T &x) {
  T r = 1;
  char ch = cin.get();
  for (; !isdigit(ch); ch = cin.get()) r = (ch == '-' ? -1 : 1);
  for (x = 0; isdigit(ch); ch = cin.get()) x = (x << 1) + (x << 3) + ch - '0';
  x *= r;
}

template <typename T, typename... Args>
void read(T &x, Args &...args) {
  read(x), read(args...);
}

template <typename T>
IL void write(T x) {
  if (x < 0) cout.put('-'), x = -x;
  if (x > 9) write(x / 10);
  cout.put(x % 10 + '0');
}

}  // namespace IO

using namespace IO;

const int N = 2e5 + 10, MOD = 998244353;

template<typename T> IL void cmax(T &a, T b) { a < b ? (a = b) : 0; }
template<typename T> IL void cmin(T &a, T b) { a > b ? (a = b) : 0; }
int add(int a, int b) { return (a + b >= MOD) ? (a + b - MOD) : (a + b); }
int sub(int a, int b) { return add(a, MOD - b); }
int mul(int a, int b) { return 1ll * a * b % MOD; }

struct Matrix {
  int a[3][3];

  Matrix() { memset(a, 0, sizeof a); }

  friend Matrix operator*(Matrix A, Matrix B) {
    Matrix C;
    for (int k = 0; k < 3; ++k)
      for (int i = 0; i < 3; ++i)
        for (int j = 0; j < 3; ++j)
          C.a[i][j] = add(C.a[i][j], mul(A.a[i][k], B.a[k][j]));
    return C;
  }
} mat0, mat1, matq, f, E;

IL void init() {
  f.a[2][0] = 1;
  mat0.a[0][0] = 1; mat0.a[0][1] = 1; mat0.a[0][2] = 1;
  mat0.a[1][1] = 1; mat0.a[2][2] = 1;
  mat1.a[0][0] = 1; mat1.a[1][0] = 1; mat1.a[1][1] = 1;
  mat1.a[1][2] = 1; mat1.a[2][2] = 1;
  matq.a[0][0] = 1; matq.a[0][1] = 1; matq.a[0][2] = 1;
  matq.a[1][0] = 1; matq.a[1][1] = 1; matq.a[1][2] = 1;
  matq.a[2][2] = 1;    
  E.a[0][0] = 1; E.a[1][1] = 1; E.a[2][2] = 1;
}

string s;
int n, q;

class SegT {
 public:
  Matrix arr[N << 2];

  SegT() {}
  
  IL void modify(Matrix &a, char c) {
  	switch (c) {
  		case '0': a = mat0; break;
  		case '1': a = mat1; break;
  		case '?': a = matq; break;
  		default: cerr << "fuck" << endl; break;
  	}
  }
  
  #define pushup arr[id] = arr[id << 1] * arr[id << 1 | 1]

  void build(int id, int l, int r) {
    if (l == r) {
    	modify(arr[id], s[l - 1]);
    	return;
    }
    int mid = (l + r) >> 1;
    build(id << 1, l, mid), build(id << 1 | 1, mid + 1, r);
    pushup;
  }
  
  void insert(int id, int l, int r, int X, char c) {
  	if (X < l || r < X) return;
  	if (l == r) { modify(arr[id], c); return; }
  	int mid = (l + r) >> 1;
  	insert(id << 1, l, mid, X, c), insert(id << 1 | 1, mid + 1, r, X, c);
  	pushup;
  }
} tree;


signed main() {
  ios::sync_with_stdio(false), cin.tie(0), cout.tie(0);
  init();
  read(n, q);
  cin >> s;
  tree.build(1, 1, n);
	for (int i = 1, pos; i <= q; ++i) {
		char c;
		read(pos); c = cin.get();
		tree.insert(1, 1, n, pos, c);
		Matrix tmp = tree.arr[1] * f;
		write(add(tmp.a[0][0], tmp.a[1][0])); cout.put('\n');
	}
  return 0;
}
```



