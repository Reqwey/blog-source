---
title: 【Solution】尼克的任务-DP
date: "2019-12-20 00:00:00"
tags:
 - DP
categories:
 - [OI, Solution]
---

[题目传送门。](https://www.luogu.com.cn/problem/P1280)

<!--more-->

## 解题思路

### 错误示范

> 第一眼看过去，便不难想到构造:
> 令 $dp[i]$ 表示从时间点 $1$ ~ $i$ 的**最大空暇时间**

```python
for i in range(1, n):
  if 第i分钟没有任务 :
      dp[i] = dp[i - 1] + 1 #意思是第i分钟可以休息，前面用dp[i - 1]决定
  else :
      for j in maps[i]: #maps[i]数组存储结束时间正好在第i分钟的每一个任务的时长
			dp[i] = max(dp[i], dp[i - maps[i][j]])
			#意思是前i分钟的休息时间只能等于这个任务做之前的休息时间

print(dp[n]) #输出dp[n]
```


但是这样做显然会有一个问题：


$dp[i]$ 的计算只考虑了**这个时间点**做完的任务, 也就是说, 可能 Nick **直到下班了也没有做完任务**, 此时做最后一项任务的时间段也会被算为空暇时间.

有没有什么改进的方法呢？答案是肯定的，那就是倒着 DP。

令 $dp[i]$ 表示从时间点 $i$ ~ $n$ 的**最大空暇时间**

这样做的好处在于, 它考虑的不再是**做完的时间**, 而变成了每个任务的**开始时间**(Nick 总不会勤奋到提前几分钟开始工作吧), 这样就完美的避免了以上那个问题.


```python
for i in range(n, 1, -1): #倒着循环
  if 第i分钟没有任务 :
      dp[i] = dp[i + 1] + 1 #意思是第i分钟可以休息，后面用dp[i + 1]决定
  else :
      for j in maps[i]: #maps[i]数组存储开始时间正好在第i分钟的每一个任务的结束时间
			dp[i] = max(dp[i], dp[i + maps[i][j]])
			#意思是后i分钟的休息时间只能等于这个任务做完后的休息时间

print(dp[1]) #输出dp[1]
```

## 完整代码

```cpp Code
#include <cstdio>
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

const int SIZE = 100001;
int n, k;

int dp[SIZE];
vector <int> maps[SIZE];

int main() {
    int start, do_time;
    cin >> n >> k;
    for (int i = 1; i <= k; i++) {
        cin >> start >> do_time;
        maps[start].push_back(do_time);
    }
    for (int i = n; i >= 1; i--) {
        int tmp = maps[i].size();
        if (tmp) {
            for (int j = 0; j < tmp; j++) {
                dp[i] = max(dp[i], dp[i + maps[i][j]]);
            }
        } else {
            dp[i] = dp[i + 1] + 1;
        }
    }
    cout << dp[1];
    return 0;
}
```
