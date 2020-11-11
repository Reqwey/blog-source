---
title: 题解 - 洛谷1280
date: "2019-12-20 00:00:00"
body: [article, comments]
cover: false
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
headimg: /assets/banner-lg1280.png
tags: 
 - DP
categories:
 - [OI, Solution]
description: "我的第二篇题解."
---

## 解题思路

### 错误示范

> 第一眼看过去，便不难想到构造:
> 令 $dp[i]$ 表示从时间点 $1$ ~ $i$ 的**最大空暇时间**

{% codeblock 伪代码(WA) lang:python line_number:true mark:1,6 %}
for i in range(1, n):
  if 第i分钟没有任务 :
      dp[i] = dp[i - 1] + 1 #意思是第i分钟可以休息，前面用dp[i - 1]决定
  else :
      for j in maps[i]: #maps[i]数组存储结束时间正好在第i分钟的每一个任务的时长
			dp[i] = max(dp[i], dp[i - maps[i][j]])
			#意思是前i分钟的休息时间只能等于这个任务做之前的休息时间

print(dp[n]) #输出dp[n]
{% endcodeblock %}

### 有问题!

{% noteblock warning %}

$dp[i]$ 的计算只考虑了**这个时间点**做完的任务, 也就是说,

有可能 Nick **直到下班了也没有做完任务**, 此时做最后一项任务的时间段也会被算为空暇时间.

{% endnoteblock %}

### 改进

{% noteblock success %}

**倒着写!!!**

令 $dp[i]$ 表示从时间点 $i$ ~ $n$ 的**最大空暇时间**

这样做的好处在于, 它考虑的不再是**做完的时间**, 而变成了每个任务的**开始时间**(Nick 总不会勤奋到提前几分钟开始工作吧), 这样就完美的避免了以上那个问题.

{% endnoteblock %}

{% codeblock 伪代码(AC) lang:python line_number:true mark:1,6 %}
for i in range(n, 1, -1): #倒着循环
  if 第i分钟没有任务 :
      dp[i] = dp[i + 1] + 1 #意思是第i分钟可以休息，后面用dp[i + 1]决定
  else :
      for j in maps[i]: #maps[i]数组存储开始时间正好在第i分钟的每一个任务的结束时间
			dp[i] = max(dp[i], dp[i + maps[i][j]])
			#意思是后i分钟的休息时间只能等于这个任务做完后的休息时间

print(dp[1]) #输出dp[1]
{% endcodeblock %}

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
