---
title: 算法笔记 - LCA - 2
date: "2020-02-06 00:00:00"
body: [article, comments]
cover: false
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
tags:
 - 数据结构
 - LCA
 - Tarjan
categories:
 - [OI, Algorithm]
description: "本文将继续学习$\\texttt{LCA}$, 只不过换一种离线的思路来解 ---- $\\texttt{Tarjan}$ 算法<br>"
---


## 算法

### 流程

前置操作分为以下几步:

1. 使用 $\texttt{DFS}$ 遍历整棵树
2. 当一个节点被访问过时, 将其标记为`1`
3. 当一个节点**及其子树**(或其本身就是**叶子节点**)都被访问过时, 将该点标记为`2`

这样, 在任意一次操作过后, 都会出现如下情况:

所有的标记为`2`的节点, 共同构成**一棵子树**

且该子树总是在整棵树的**左下处**

从动态的角度来看, 就是一棵全为`2`的子树, 从左下角的那个叶子节点处缓缓地"长"了出来

这样有什么好处呢

这个性质可以帮助我们很好地离线解决$\texttt{LCA}$问题

### Solve

建立一个**并查集**: `father[]`

在搜索的**返回**过程中, 将已经变成`2`的那个节点的`father`设为它的父亲节点(相当于把该节点"缩"到了其父亲节点上)

在搜索的返回前, 即遍历完该节点的子树后, 判断该节点$u$有没有被"提问"过

如果有, 假设提问为$lca(u, v)$, 则标记答案为`getfather(v)`, 其中`getfather()`函数为并查集自带的查找祖先节点的函数

* 为什么?

根据上边的结论, 此时的`getfather(v)`即为那棵全为`2`的子树的根节点$root'$,  $\because u$和$v$**均在这棵子树上**, $\therefore root'$为$u$和$v$的**公共祖先**

考虑$\texttt{DFS}$序, $\because \texttt{DFS}$访问完一个节点不会马上往上走, 而是会去**遍历其它节点**, $\therefore root'$以下没有一个$root''$满足是$u$和$v$的**公共祖先** $\therefore lca(u, v) = root'$

## 代码

```cpp Code
#include <cstdio>
#include <iostream>
#include <vector>

using namespace std;

const int SIZE = 5e6 + 1;

struct edge
{
    int to_node, id;
    edge(int t, int i): to_node(t), id(i) {}
    ~edge() = default;
};

vector<int> edges[SIZE];
vector<edge> querys[SIZE];
int father[SIZE], mark[SIZE], ans[SIZE];

int n, m, s;

int getfa(int x)
{
    if (father[x] == x)
        return x;
    else
        return father[x] = getfa(father[x]);
}

void tarjan(int x)
{
    mark[x] = 1;
    for (auto i = edges[x].begin(); i != edges[x].end(); i++)
    {
        if (mark[*i])
            continue;
        tarjan(*i);
        father[*i] = x;
    }
    for (auto i = querys[x].begin(); i != querys[x].end(); i++)
    {
        int y = (*i).to_node, id = (*i).id;
        if (mark[y] == 2)
            ans[id] = getfa(y);
    }
    mark[x] = 2;
}

int main()
{
    int u, v;
    scanf("%d%d%d", &n, &m, &s);
    for (int i = 1; i <= n; i++)
    {
        father[i] = i;
        mark[i] = 0;
    }
    for (int i = 1; i < n; i++)
    {
        scanf("%d%d", &u, &v);
        edges[u].emplace_back(v);
        edges[v].emplace_back(u);
    }
    int x, y;
    for (int i = 1; i <= m; i++)
    {
        scanf("%d%d", &x, &y);
        if (x == y)
            ans[i] = 0;
        else
        {
            querys[x].emplace_back(edge(y, i));
            querys[y].emplace_back(edge(x, i));
        }
    }
    tarjan(s);
    for (int i = 1; i <= m; i++)
        printf("%d\n", ans[i]);
    return 0;
}
```

## 时间复杂度

$$ \Theta{(n + m)} $$

搜索过程是$\Theta{(n)}$的, 而其中的求解过程可以单独**拆出来看**, 它就是一个$\Theta{(m)}$

但我的存边方式似乎不够优秀, 导致**比上一篇文章还慢**!!!

下次一定要用邻接表55555

<btn regular>[评测记录](https://www.luogu.com.cn/record/30231788)</btn>

用一张表来分析分析求$\texttt{LCA}$的各种算法的优劣之处:

|   算法   |            优劣            |       时间复杂度        |   评测结果   |
| :------: | :------------------------: | :---------------------: | :------: |
|   朴素   |        比较好想2333        |     $\Theta{(nm)}$      | <red>TLE</red> |
| 倍增优化 | 也比较好想, 但是较容易敲挂 | $\Theta{((n + m)logn)}$ | <green>AC</green>  |
|  Tarjan  |    比较精妙, 也不太好写    |    $\Theta{(n + m)}$    | <green>AC</green>  |

::: danger

**注意!**

以上评测结果属于理想情况, 如果碰到一些爱卡常的毒瘤出题人(如<btn>[某谷](https://www.luogu.com.cn)</btn>的神鱼), 特别是你的存边方式像我一样, 那我就不知道了

:::
