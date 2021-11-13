---
title: '【题解】[NOI Online #1 入门组] 文具订购-DP'
date: "2020-3-8 00:00:00"
tags:
 - 动态规划，DP
categories:
 - [OI, Solution]
---



[题目传送门。](https://www.luogu.com.cn/problem/P6188)

<!--more-->


**分类讨论**


可以枚举三个数中**最小**的


下面以 $a$ 最小的情况为例

* $\because 7a+4b+3c=n$
* $\therefore 7a=n-4b-3c$
* 由于我们要考虑 $a+b+c$ 最大. 因为 $b$ 的系数比 $c$ 要大, 而**和为定值**, 所以当 $a$ 固定时, $b$ **越小越好**
* 所以我们就可以令 $b$ 为 $a, a+1, a+2$ 中的一个(为了满足 $c$ 是整数, 找一个值使得 $n-4b-7a \equiv 0 \pmod {3}$)

将上面的方法重复套到 $b$ 和 $c$ 中, 跑三遍 `for` (有点丑)即可


最后然后别忘了**校验得出来的值是否合理**


```cpp Code
#include <cstdio>
#include <iostream>

#define min3(a, b, c) std::min(std::min(a, b), c)

const int coeff_a = 7, coeff_b = 4, coeff_c = 3;

int main()
{
    int n;
    int ans_a = -1, ans_b = -1, ans_c = -1;

    scanf("%d", &n);
    
    for (int a = 0; a <= n; a++) // a <= b, c
    {
        int b = a, c;
    
        while (((n - a * coeff_a - b * coeff_b) % coeff_c + coeff_c) % coeff_c != 0)
            b++;
        c = (n - a * coeff_a - b * coeff_b) / coeff_c;
    
        if (c < 0 || c < a)
            break; // 矛盾
    
        if (min3(ans_a, ans_b, ans_c) < a)
            ans_a = a, ans_b = b, ans_c = c;
        else if (min3(ans_a, ans_b, ans_c) == a && ans_a + ans_b + ans_c < a + b + c)
            ans_a = a, ans_b = b, ans_c = c;
    }
    
    for (int b = 0; b <= n; b++) // b <= a, c
    {
        int a = b, c;
    
        while (((n - a * coeff_a - b * coeff_b) % coeff_c + coeff_c) % coeff_c != 0)
            a++;
        c = (n - a * coeff_a - b * coeff_b) / coeff_c;
    
        if (c < 0 || c < b)
            break; // 矛盾
    
        if (min3(ans_a, ans_b, ans_c) < b)
            ans_a = a, ans_b = b, ans_c = c;
        else if (min3(ans_a, ans_b, ans_c) == b && ans_a + ans_b + ans_c < a + b + c)
            ans_a = a, ans_b = b, ans_c = c;
    }
    
    for (int c = 0; c <= n; c++) // c <= a, b
    {
        int a = c, b;
    
        while (((n - a * coeff_a - c * coeff_c) % coeff_b + coeff_b) % coeff_b != 0)
            a++;
        b = (n - a * coeff_a - c * coeff_c) / coeff_b;
    
        if (b < 0 || b < c)
            break; // 矛盾
    
        if (min3(ans_a, ans_b, ans_c) < c)
            ans_a = a, ans_b = b, ans_c = c;
        else if (min3(ans_a, ans_b, ans_c) == c && ans_a + ans_b + ans_c < a + b + c)
            ans_a = a, ans_b = b, ans_c = c;
    }
    
    if (ans_a == -1)
        puts("-1");
    else
        printf("%d %d %d", ans_a, ans_b, ans_c);
    return 0;
}
```