---
title: NOIp集训-数学基础1
date: 2020-08-30 22:24:00
body: [article, comments]
cover: false
icons: [far fa-edit blue]
mathjax: true
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

> **注** 可以用这个方法逆过来，即“倍数法”求解1～N每个数的正约数集

### 最大公约数gcd

**更相减损术**

$$ \forall a,b \in \mathbb {N}, a \geq b, 有 gcd(a,b) = gcd(b, a-b) = gcd(a, a-b) $$

**欧几里德算法**

$$ \forall a,b \in \mathbb {N}, a \geq b,  b \neq 0，有 gcd(a,b) = gcd(b, a%b) $$

blablalba
