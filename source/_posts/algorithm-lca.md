---
title: 算法笔记 - LCA - 1
date: "2020-01-28 00:00:00"
body: [article, comments]
cover: false
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
photoo: true
photourl: https://rmt.dogedoge.com/fetch/royce/storage/SegmentTree2/cover.png?fmt=webp
tags:
 - 数据结构
 - LCA
 - 倍增
categories:
 - [OI, Algorithm]
description: "这篇文章主要是作者学习$\\texttt{LCA}$算法的一些心得体会"
---

## 定义

一个点的**祖先**: 这个比较好理解吧, 就是从该节点出发, 一路**向上**走能碰到的就是其祖先了

两个点的**公共祖先**: 就是**同一棵树**上两个节点的**祖先集合**中的**交集**

**最近公共祖先**就是这个交集里面最**靠下**的

{% noteblock info %}
注意到以上的表述中经常出现**向上**或**靠下**等字眼, 说明求最近公共祖先的算法肯定与求节点的**高度**有关
{% endnoteblock %}

## 朴素算法法求LCA

想象一下这个过程: 

* 两个节点先跳到**同一个高度**

* 如果两节点相遇 (即原先两个节点存在**祖孙关系**), 该点即为LCA, 退出

* 否则, 一起**向上跳**, 直到相遇

## 优化

> 以上这种一层一层跳的方法太慢了, 面对一棵巨大的树时会跑得巨慢, 我们要尝试优化这个过程
>
> 试想一下: 如果用**倍增**跳呢? 是不是速度立刻就上去了?

### 一些前置工作

* 用倍增的思想求出节点 $x$ 往上跳 $2^p$ 步后可以到达的节点, 存在 $fa[x][p]$ 中
* 用 $\texttt{BFS}$ 或 $\texttt{DFS}$ 求出节点 $x$ 的祖先, 存在 $deep[x]$ 中

这样, 每次跳的时候, 可以通过比较 $deep[a]$ 是否等于 $deep[b]$ 来判断**是否在同一层**, 并增加跳的长度, 把朴素算法的时间复杂度优化到 $log$ 级别

### 具体实现

从**根节点**遍历整棵树, 顺便记录一下子节点的信息

```cpp Code
node nod = que.front(); que.pop();
for (auto i = edges[nod.x].begin(); i != edges[nod.x].end(); i++)
{
    if ((*i) != nod.fa) // 该节点不为父节点
    {
        que.emplace(node(*i, nod.x));
        deep[*i] = deep[nod.x] + 1; // 求deep[]数组
        fa[*i][0] = nod.x;
        for (int j = 1; (1 << j) <= deep[*i]; j++) // 倍增法求fa[]数组
            fa[*i][j] = fa[fa[*i][j - 1]][j - 1];
    }
}
```

### 开始愉快地跳跃

* 先跳到同一高度

  ```cpp Code
  if (deep[a] < deep[b])
      swap(a, b);
  if (!deep[b])
      return b;
  for (int i = MAXP; i >= 0; i--)
  {
      if (deep[fa[a][i]] >= deep[b])
          a = fa[a][i];
      if (a == b)
          return a;
  }
  ```

* 两个点一起往上跳

  ```cpp Code
  for (int i = MAXP; i >= 0; i--)
  {
      if (fa[a][i] != fa[b][i])
      {
          a = fa[a][i];
          b = fa[b][i];
      }
  }
  return fa[a][0];
  ```

## 上代码

```cpp Code
#include <cstdio>
#include <vector>
#include <queue>
#include <iostream>
#include <cmath>

using namespace std;

struct node
{
    int x, fa;
    node(int xx = 0, int ffa = 0) : x(xx), fa(ffa) {}
    ~node() = default;
};

const int SIZE = 5e6 + 1, MAXP = 20;
vector<int> edges[SIZE];
queue<node> que;
int n, m, s;
int fa[SIZE][MAXP + 1], deep[SIZE];

void bfs(int st)
{
    que.emplace(node(st, 0));
    deep[st] = 0;
    while (!que.empty())
    {
        node nod = que.front(); que.pop();
        for (auto i = edges[nod.x].begin(); i != edges[nod.x].end(); i++)
        {
            if ((*i) != nod.fa)
            {
                que.emplace(node(*i, nod.x));
                deep[*i] = deep[nod.x] + 1;
                fa[*i][0] = nod.x;
                for (int j = 1; (1 << j) <= deep[*i]; j++)
                    fa[*i][j] = fa[fa[*i][j - 1]][j - 1];
            }
        }
    }
}

int lca(int a, int b)
{
    if (deep[a] < deep[b])
        swap(a, b);
    if (!deep[b])
        return b;
    for (int i = MAXP; i >= 0; i--)
    {
        if (deep[fa[a][i]] >= deep[b])
            a = fa[a][i];
        if (a == b)
            return a;
    }
    for (int i = MAXP; i >= 0; i--)
    {
        if (fa[a][i] != fa[b][i])
        {
            a = fa[a][i];
            b = fa[b][i];
        }
    }
    return fa[a][0];
}

int main()
{
    int u, v;
    scanf("%d%d%d", &n, &m, &s);
    for (int i = 1; i < n; i++)
    {
        scanf("%d%d", &u, &v);
        edges[u].emplace_back(v);
        edges[v].emplace_back(u);
    }
    bfs(s);
    int a, b;
    for (int i = 1; i <= m; i++)
    {
        scanf("%d%d", &a, &b);
        printf("%d\n", lca(a, b));
    }
    return 0;
}
```

## 时间复杂度

$$\Theta{(n + nlog_2n + mlog_2n)} = \Theta{((n + m)logn)}$$

## 版权信息

{% noteblock link %}
封面图片来源: [Icons8](https://icons8.com/) & [JalenChuh](https://blog.jalenchuh.cn/posts/SegmentTree2.html)
{% endnoteblock %}
