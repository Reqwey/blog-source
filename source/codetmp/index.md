---
title: 未完成的代码
body: [article, grid, comments]
meta:
  header: false
  footer: false
layout: page
valine:
  placeholder: 有什么想对我说的呢？
sidebar: false
---

```cpp
#include <cstdio>
#include <iostream>

using namespace std;

#define NU -1
#define ALL_ZERO 0
#define ALL_ONE 1
#define ALL_DIF 2
#define QUE_ALL 3
#define QUE_CTN 4

#define left(x) SegTree[x].l
#define right(x) SegTree[x].r
#define mid(x) ((left(x) + right(x)) >> 1)
#define len(x) (right(x) - left(x) + 1)
#define lzy(x) SegTree[x].lazy_op
#define ll(x) (mid(x) - SegTree[x << 1].cnt_1.r_num + 1)
#define rr(x) (mid(x) + SegTree[(x << 1) + 1].cnt_1.l_num)
#define pushdown(x) \
if (len(x) > 1) insert(left(x), mid(x), x << 1, lzy(x)), insert(mid(x) + 1, right(x), (x << 1) + 1, lzy(x));

const int SIZE = 5000001;

int arr[SIZE / 5];

struct Node
{
    int l_num, r_num, m_num;
    Node(int l = 0, int r = 0, int m = 0) : l_num(l), r_num(r), m_num(m) {}
    ~Node() {}
};

struct SegT
{
    int l, r;
    int s_num, lazy_op;
    Node cnt_1, cnt_0;
} SegTree[SIZE];

void build(int l, int r, int x);
void change(int x, int op);
inline void work_0(int x);
inline void work_1(int x);
inline void try_pushdown(int x, int op);
void insert(int l, int r, int x, int op);
int query_n(int l, int r, int x);
int query_l(int l, int r, int x);

int main()
{
    int n, m;
    int op, l, r;
    scanf("%d%d", &n, &m);
    for (int i = 0; i < n; i++)
        scanf("%d", arr + i);
    build(0, n - 1, 1);
    for (int i = 1; i <= m; i++)
    {
        // puts("------DEBUG------");
        // for (int j = 0; j < n; j++)
        //     printf("%d ", query_n(j, j, 1));
        // puts("");
        // puts("----END DEBUG----");
        scanf("%d%d%d", &op, &l, &r);
        if (op == ALL_DIF || op == ALL_ONE || op == ALL_ZERO)
            insert(l, r, 1, op);
        else if (op == QUE_ALL)
            printf("%d\n", query_n(l, r, 1));
        else
            printf("%d\n", query_l(l, r, 1));
    }
    return 0;
}

void build(int l, int r, int x)
{
    lzy(x) = NU;
    if (l == r)
    {
        left(x) = right(x) = l;
        SegTree[x].s_num =
            SegTree[x].cnt_1.l_num =
                SegTree[x].cnt_1.m_num =
                    SegTree[x].cnt_1.r_num = arr[l];
        SegTree[x].cnt_0.l_num =
            SegTree[x].cnt_0.m_num =
                SegTree[x].cnt_0.r_num = (!arr[l]);
    }
    else
    {
        left(x) = l, right(x) = r;
        build(l, mid(x), x << 1);
        build(mid(x) + 1, r, (x << 1) + 1);
        SegTree[x].s_num = SegTree[x << 1].s_num + SegTree[(x << 1) + 1].s_num;
        work_0(x);
        work_1(x);
    }
}

void change(int x, int op)
{
    switch (op)
    {
    case ALL_ZERO:
        SegTree[x].s_num = 0;
        SegTree[x].cnt_0.l_num =
            SegTree[x].cnt_0.m_num =
                SegTree[x].cnt_0.r_num = len(x);
        SegTree[x].cnt_1.l_num =
            SegTree[x].cnt_1.m_num =
                SegTree[x].cnt_1.r_num = 0;
        break;
    case ALL_ONE:
        SegTree[x].s_num = len(x);
        SegTree[x].cnt_0.l_num =
            SegTree[x].cnt_0.m_num =
                SegTree[x].cnt_0.r_num = 0;
        SegTree[x].cnt_1.l_num =
            SegTree[x].cnt_1.m_num =
                SegTree[x].cnt_1.r_num = len(x);
        break;
    case ALL_DIF:
        swap(SegTree[x].cnt_1, SegTree[x].cnt_0);
        SegTree[x].s_num = len(x) - SegTree[x].s_num;
        break;
    default:
        break;
    }
}

inline void work_0(int x)
{
    SegTree[x].cnt_0.l_num = SegTree[x << 1].cnt_0.l_num;
    SegTree[x].cnt_0.r_num = SegTree[(x << 1) + 1].cnt_0.r_num;
    SegTree[x].cnt_0.m_num = SegTree[x << 1].cnt_0.r_num + SegTree[(x << 1) + 1].cnt_0.l_num;
    if (SegTree[x << 1].cnt_0.r_num == len(x << 1) && SegTree[(x << 1) + 1].cnt_0.l_num == len((x << 1) + 1))
        SegTree[x].cnt_0.l_num = SegTree[x].cnt_0.r_num = len(x);
}

inline void work_1(int x)
{
    SegTree[x].cnt_1.l_num = SegTree[x << 1].cnt_1.l_num;
    SegTree[x].cnt_1.r_num = SegTree[(x << 1) + 1].cnt_1.r_num;
    SegTree[x].cnt_1.m_num = SegTree[x << 1].cnt_1.r_num + SegTree[(x << 1) + 1].cnt_1.l_num;
    if (SegTree[x << 1].cnt_1.r_num == len(x << 1) && SegTree[(x << 1) + 1].cnt_1.l_num == len((x << 1) + 1))
        SegTree[x].cnt_1.l_num = SegTree[x].cnt_1.r_num = len(x);
}

inline void try_pushdown(int x, int op)
{
    if (op != ALL_DIF)
        lzy(x) = op;
    else if (lzy(x) != ALL_DIF)
        lzy(x) ^= 1;
}

void insert(int l, int r, int x, int op)
{
    if (l <= left(x) && right(x) <= r)
    {
        if (lzy(x) != NU)
            try_pushdown(x, op);
        else
            lzy(x) = op;
        change(x, lzy(x));
    }
    else
    {
        if (lzy(x) != NU)
        {
            pushdown(x);
            lzy(x) = NU;
        }
        if (l <= mid(x))
            insert(l, r, x << 1, op);
        if (r > mid(x))
            insert(l, r, (x << 1) + 1, op);
        SegTree[x].s_num = SegTree[x << 1].s_num + SegTree[(x << 1) + 1].s_num;
        work_0(x);
        work_1(x);
    }
}

int query_n(int l, int r, int x)
{
    if (l <= left(x) && right(x) <= r)
        return SegTree[x].s_num;
    else
    {
        if (lzy(x) != NU)
        {
            pushdown(x);
            lzy(x) = NU;
        }
        int ans = 0;
        if (l <= mid(x))
            ans += query_n(l, r, x << 1);
        if (r > mid(x))
            ans += query_n(l, r, (x << 1) + 1);
        return ans;
    }
}

int query_l(int l, int r, int x)
{
    // printf("DEBUG: query_l(l = %d, r = %d, x = %d (lx = %d, rx = %d, ll = %d, rr = %d)\n", l, r, x, left(x), right(x), ll(x), rr(x));
    if (lzy(x) != NU)
    {
        pushdown(x);
        lzy(x) = NU;
    }

    if (left(x) == right(x))
        return SegTree[x].s_num;
    else if (ll(x) <= l && r <= rr(x))
        return r - l + 1;
    else if (r <= mid(x))
        return query_l(l, r, x << 1);
    else if (l > mid(x))
        return query_l(l, r, (x << 1) + 1);
    else
    {
        int ans1 = query_l(l, mid(x), x << 1),
            ans2 = query_l(mid(x) + 1, r, (x << 1) + 1);
        int tl = max(l, ll(x)), tr = min(r, rr(x));
        return max(max(ans1, ans2), tr - tl + 1);
    }
}
```