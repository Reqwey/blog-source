---
title: 算法笔记 - 最短路
date: "2020-5-7 00:00:00"
body: [article, comments]
cover: false
icons: [far fa-fire red]
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
tags:
 - 图论
 - Floyd
 - Dijkstra
 - SPFA
categories:
 - [OI, Algorithm]
description: "五一假期里, 三中举办的算法在线课程之最短路算法"
---

最短路算法分为单源最短路和多源最短路两种, 具体又大致分为三种算法: $\texttt{Floyd}$ , $\texttt{Dijkstra}$ 以及 $\texttt{SPFA}$

## 多源最短路

可以随便求两个点之间的最短距离

### 只有五行的算法 - Floyd算法

```cpp Floyd核心算法
int g[N][N];
void floyd(int n) {
    for (int k = 1; k <= n; ++k)
        for (int i = 1; i <= n; ++i)
            for (int j = 1; j <= n; ++j)
                g[i][j] = min(g[i][j], g[i][k] + g[k][j]);
}
```

这个算法的思路有两种, 一个是**松弛思想**(重复执行并尝试通过拓展路径来降低长度), 一个是**DP思想**

**DP思想**: 令$g[k][i][j]$表示从$i$到$j$的路径, 对于其中经过点的编号集合$V$(不包括$i$和$j$),有 $V \in \{ 1...k \} $ 的最短路长度

则有转移方程:

$$ g[k][i][j] = min(g[k-1][i][j], g[k-1][i][k] + g[k-1][k][j]) $$

即$g[k][i][j]$既可以不经过第$k$号点, 从前$k-1$个点中任何一个转过来, 也可以经过第$k$号点

然后发现多了一维. 直接压缩掉即可

## 单源最短路

告诉你确定的两点, 以更快的速度求出两点之间最短路, 用$\texttt{Floyd}$效率就太低了

### BFS(广搜)

适用于边权都为1的情况, 跑得飞快. 简单, 不贴代码

### Dijkstra

大名鼎鼎的单源最短路算法

#### Original

```cpp 不加优化的朴素Dij, 看代码就明白了
#include <cstdio>
#include <cstring>
#include <queue>
#include <vector>

const int N = 10005;

int d[N], vis[N], n, m, s;

std::vector< std::pair<int, int> > g[N];

void dijkstra()
{
    memset(d, 0x3f, sizeof(d));
    memset(vis, 0, sizeof(vis));
    d[s] = 0;
    while (true)
    {
        int u = -1;
        for (int i = 1; i <= n; i++)
        {
            if ((!vis[i]) && (u == -1 || d[i] < d[u]))
                u = i;
        }
        if (u == -1) // 找不到可以扩展的点
            break;
        vis[u] = 1;
        for (int i = 0; g[u].size(); i++)
        {
            int v = g[u][i].first, w = g[u][i].second;
            if (d[v] > d[u] + w)
                d[v] = d[u] + w;
        }
    }
}

int main()
{
    int n, m;

    scanf("%d%d%d", &n, &m, &s);
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        scanf("%d%d%d", &u, &v, &w);
        g[u].emplace_back(std::make_pair(v, w));
        g[v].emplace_back(std::make_pair(u, w));
    }
    dijkstra();
    return 0;
}
```

:::note

采用`vector`邻接表存图, 比赛时不建议使用

:::



#### 堆优化

加快每次选择一个距离最小的点的过程, 用一个堆(优先队列)

```cpp 堆优化版Dijkstra
#include <cstdio>
#include <cstring>
#include <queue>
#include <vector>

const int N = 10005;

int d[N], vis[N], n, m, s;

std::vector< std::pair<int, int> > g[N];

struct node
{
    int d, id;
    bool operator< (const node &a) const {
        return d > a.d;
    }

    node(int d = 0, int id = 0): d(d), id(id) {}
    ~node() {}
};


std::priority_queue<node> q;

void dijkstra()
{
    memset(d, 0x3f, sizeof(d));
    memset(vis, 0, sizeof(vis));
    d[s] = 0;
    q.push(node(0, s));
    while (!q.empty())
    {
        int u = q.top().id;
        q.pop();
        if (vis[u])
            continue;
        vis[u] = 1;
        for (int i = 0; i < g[u].size(); i++)
        {
            int v = g[u][i].first, w = g[u][i].second;
            if (d[v] > d[u] + w)
            {
                d[v] = d[u] + w;
                q.push(node(d[v], v));
            }
        }
    }
}

int main()
{
    int n, m;

    scanf("%d%d%d", &n, &m, &s);
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        scanf("%d%d%d", &u, &v, &w);
        g[u].push_back(std::make_pair(v, w));
        g[v].push_back(std::make_pair(u, w));
    }
    dijkstra();
    return 0;
}
```

### SPFA

::: quote

Shortest Path Fastest Algorithm

:::

好吧, 多么霸气的名字...

![image.png](https://i.loli.net/2020/05/07/kSKZGHbfIoRC7q8.png)

但是我觉得正常出题人不会把数据卡到那么死, 而且它还有特异功能----判负环

```cpp SPFA
bool spfa(int s)
{
    while (!q.empty())
        q.pop();
    memset(qcnt, 0, sizeof(qcnt));
    memset(d, 0x3f, sizeof(d));
    memset(vis, 0, sizeof(vis));

    d[s] = 0;
    vis[s] = 1;
    q.push(node(0, s));

    while (!q.empty())
    {
        int u = q.front().id;
        q.pop();
        vis[u] = 0;
        for (int i = 0; i < g[u].size(); i++)
        {
            int v = g[u][i].first, w = g[u][i].second;
            if (d[v] > d[u] + w)
            {
                d[v] = d[u] + w;
                qcnt[v] = qcnt[u] + 1;
                if (qcnt[v] > n)
                    return true;
                else if (!vis[v])
                {
                    q.push(node(d[v], v));
                    vis[v] = 1;
                }
            }
        }
    }
    return false;
}
```

以上代码专门用来判断负环, 如果一个点重复入队了超过$n$次, 则说明形成负环, 返回`true`, 否则返回`false`

## 拓展: 各种存边方式

做图论题, 怎么能不知道如何存边? 以下给出了常见的几种存边方法

### 邻接矩阵

好说, 一个二维数组嘛

### 邻接表

较为节省空间的存边方式

#### 链式前向星

```cpp 链式前向星
#include <cstdio>
#include <cstring>

const int N = 1005, M = 1005;

struct myEdge
{
    int to, len, nxt;
    myEdge(int to = 0, int len = 0, int nxt = 0) : to(to), len(len), nxt(nxt) {}
    ~myEdge() {}
};

myEdge e[M * 2];
int ecnt, head[N];

void add(int u, int v, int w)
{
    e[ecnt].to = v;
    e[ecnt].len = w;
    e[ecnt].nxt = head[u];
    head[u] = ecnt++;
}

int main()
{
    int n, m;
    memset(head, -1, sizeof(head));

    scanf("%d%d", &n, &m);
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        scanf("%d%d%d", &u, &v, &w);
        add(u, v, w);
        add(v, u, w);
    }

    int uu;
    scanf("%d", &uu);
    //                      i != -1
    for (int i = head[uu]; ~i; i = e[i].nxt)
    {
        int v = e[i].to;
        printf("%d\n", v);
    }
}
```

#### vector

```cpp vector模拟邻接表
#include <cstdio>
#include <cstring>
#include <vector>

const int N = 1005, M = 1005;

struct myEdge
{
    int to, len;
    myEdge(int to = 0, int len = 0) : to(to), len(len) {}
    ~myEdge() {}
};

std::vector<myEdge> g[N];

int main()
{
    int n, m;

    scanf("%d%d", &n, &m);
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        scanf("%d%d%d", &u, &v, &w);
        g[u].emplace_back(myEdge(v, w));
        g[v].emplace_back(myEdge(u, w));
    }

    int uu;
    scanf("%d", &uu);

    for (auto &i : g[uu])
    {
        printf("%d\n", i.to);
    }
    return 0;
}
```

::: warning

* vector 看起来高大上, 实际跑得贼慢, 慎用

* 代码中采用`auto`和`emplace_back()`等C++11关键字, 在NOIp系列比赛中禁止使用

:::
