---
title: 【题解】[JSOI2008]最大数-ST表
date: "2020-01-15 00:00:00"
tags:
 - ST表
 - 倍增
categories:
 - [OI, Solution]
---


[题目传送门。](https://www.luogu.com.cn/problem/P1198)

<!--more-->

## 解题思路

* $\texttt{ST}$ 表

令 $st[ i ][ j ]$ 为 $\max \limits_{1 \leq t \leq (i + 2^j - 1)} arr[t]$

每在序列 $arr$ 的后面加入一个新值(假设是 $arr[ n ]$)时

它只会影响到一类 $st[ i ][ j ]$ 当且仅当

$$i + 2^j - 1 = n$$

也就是说，只会影响到所有**终点为$n$的区间**

将上述式子变形得:

$$i = n - 2^j + 1$$

所以，我们可以穷举这个$j$，使得 $1 \leq 2^j \leq n$

那么就可以定出这个长为$j$，终点为$n$的区间:

$$st[ i ][ j ] = st[ n - 2^j + 1 ][j]$$

## Talk is cheap, show me the code.

```cpp
void insert(ll num) {
    n++;
    st[n][0] = num; //初始化
    for (int i = 1; (1 << i) <= n; i++) {
        int tmp = n - (1 << i) + 1;
        st[tmp][i] = max(st[tmp][i - 1], st[tmp + (1 << (i - 1))][i - 1]);
        //这里的tmp其实就是原文中的i, 同理，这里的i是原文中的j
    }
}
```

```cpp
// 返回的是长度为 l ，终点为 n 的区间最大值
ll solve(int l) {
    int k = (int)(log(double(l)) / log(2.0));
    return max(st[n - l + 1][k], st[n - (1 << k) + 1][k]);
}
```

```cpp
#include <cstdio>
#include <iostream>
#include <cmath>
#include <algorithm>

using namespace std;
typedef long long ll;
const int ASK_SZ = 200001, LOG = 50;

int m;
ll d, last_ans;
int n;
ll st[ASK_SZ][LOG];

ll solve(int l);
void insert(ll num);

int main() {
    char op;
    ll num;
    cin >> m >> d;
    for (int i = 1; i <= m; i++) {
        cin >> op >> num;
        if (op == 'Q') {
            cout << (last_ans = solve(num)) << '\n';
        }
        else {
            insert((num + last_ans) % d);
        }
    }
    return 0;
}

ll solve(int l) {
    int k = (int)(log(double(l)) / log(2.0));
    return max(st[n - l + 1][k], st[n - (1 << k) + 1][k]);
}

void insert(ll num) {
    n++;
    st[n][0] = num;
    for (int i = 1; (1 << i) <= n; i++) {
        int tmp = n - (1 << i) + 1;
        st[tmp][i] = max(st[tmp][i - 1], st[tmp + (1 << (i - 1))][i - 1]);
    }
}
```