---
title: NOIp集训-数学基础1
date: 2020-08-30 22:24:00
body: [article, comments]
cover: false
icons: [far fa-edit blue]
mathjax: true
headimg: /assets/newbanner92.png
tags:
  - math
  - prime
  - mod-function
categories:
 - [OI, Algorithm]
description: "OI中有关数学的基础算法01: 素数，约数，同余初步, editing..."
---

## 素数

### 判定素数-试除法

```cpp
bool is_prime(int n)
{
    if (n < 2)
        return false;
    for (int i = 2; i <= sqrt(n); i++)
        if (n % i == 0)
            return false;
    return true;
}
```


### 素数筛法

#### Eratosthenes 筛法

从 2 开始，由小到大扫描每个数 $x$ ，把它的倍数 $2x,3x,...,\lfloor N/x \rfloor * x$ 标为和数

如果未标记，则为素数

观察到，小于 $x^2$ 的 $x$ 倍数已经被标记过，因此从 $x^2$ 开始，把 $x^2,x * (x + 1),...,\lfloor N/x \rfloor * x$ 标为和数

> **不足** 仍存在重复筛的情况，如12会被2和3同时筛到，无法达到线性时间复杂度

#### 线性筛法

不重不漏地标记和数，降低时间复杂度

方法：从最小质因子入手，保证每个和数在遍历到它的最小质因子时就已经被标记了

```cpp 第一种方法
vector<int> primes;
bool not_prime[N];
int que[N];

void get_prime(int n, int maxn)
{
    for (int i = 2; i <= n && primes.size() <= maxn; i++)
    {
        if (!not_prime[i])
            primes.push_back(i);
        for (vector<int>::iterator it = primes.begin(); it != primes.end() && i * (*it) <= n; it++)
        {
            not_prime[i * (*it)] = 1;
            if (i % (*it) == 0)
            // 说明这以后的i * (*(it + x)) 中包含的最小质因子将不再是*(it + x), 而是*it
                break;
        }
    }
}
```

```cpp 第二种
int v[N], prime[N];
m = 0;
void primes(int n)
{
    // v 为最小质因子
    for (int i = 2; i <= n; i++)
    {
        if (v[i] == 0) { v[i] = i; prime[++m] = i; } // i is prime
        for (int j = 1; j <= m; j++)
        {
            if (prime[j] > v[i] || prime[j] > n / i) break;
            v[i * prime[j]] = prime[j];
        }
    }
}
```

## 约数


### 分解质因数-试除法

* 如果 $n$ 为和数，则它的所有质因数小于等于 $\sqrt{n}$
* 如果 $n$ 为和数，则它的质因子为本身

```cpp
struct num
{
    int n, cnt;
} p[100001];

void divide(int n)
{
    m = 0;
    for (int i = 2; i * i <= n; i++)
    {
        if (!(n % i))
        {
            p[++m].n = i, p[m].cnt = 0;
            while (!(n % i))
                n /= i, p[m].cnt++;
        }
    }
    if (n > 1)
        p[++m].n = n, p[m].cnt = 1;
}
```

> **推论** 一个整数 $N$ 的约数个数上界为 $2\sqrt{N}$
>
> **注** 可以用这个方法逆过来，即“倍数法”求解1～N每个数的正约数集

### 最大公约数gcd

**更相减损术**

$$ \forall a,b \in \mathbb {N}, a \geq b, 有 gcd(a,b) = gcd(b, a-b) = gcd(a, a-b) $$

**欧几里德算法**

$$ \forall a,b \in \mathbb {N}, a \geq b,  b \neq 0，有 gcd(a,b) = gcd(b, a \ \text{mod} \ b) $$

```cpp
int gcd(int a, int b)
{
    return b ? gcd(b, a % b) : a;
}
```

### 欧拉函数

* 1 ～ N 中与 N 互质的数的个数
* 记为 $\varphi(N)$

$$ \varphi(N) = N * \prod_{质数p|N} (1 - \frac{1}{p}) $$

记住证法是容斥原理

例如 `N` 有两个质因子 `p` 和 `q`

则要从 `1 ～ N` 中扣掉 `p` 的倍数和 `q` 的倍数，然后加上 `lcm(p,q)` 的倍数

$$ N - \frac{N}{p} - \frac{N}{q} + \frac{N}{pq} = N(1 - \frac{1}{p})(1 - \frac{1}{q}) $$

所以在分解质因数的同时可以求出欧拉函数

```cpp

int phi(int n)
{
    ans = n;
    m = 0;
    for (int i = 2; i * i <= n; i++)
    {
        if (!(n % i))
        {
            ans = ans / i * (i - 1)
            while (!(n % i))
                n /= i, p[m].cnt++;
        }
    }
    if (n > 1)
        ans = ans / n * (n - 1);
    return n;
}
```

#### 欧拉筛

这里运用的了两个性质

1. 设 $p$ 为质数, 若 $p|n$ 且 $p^2|n$, 则 $\varphi(n)=\varphi(n/p)*p$
1. 设 $p$ 为质数, 若 $p|n$ 但 $p^2 \nmid n$, 则 $\varphi(n)=\varphi(n/p)*(p-1)$

证明方法见《算法竞赛进阶指南》P146～

我们根据这个原理结合线性筛素数可以写出线性筛euler的做法

```cpp
int v[MAX_N], prime[MAX_N], phi[MAX_N]
void euler(int n)
{
    // v 为最小质因子
    for (int i = 2; i <= n; i++)
    {
        if (v[i] == 0)
        {
            v[i] = i;
            prime[++m] = i;
            phi[i] = i - 1;
        } // i is prime
        for (int j = 1; j <= m; j++)
        {
            if (prime[j] > v[i] || prime[j] > n / i) break;
            v[i * prime[j]] = prime[j];
            phi[i * prime[j]] = phi[i] * (i % prime[j] ? prime[j] - 1 : prime[j]);
        }
    }
}
```

### 同余

#### 从费马小定理到扩展欧拉定理

> blablabla
