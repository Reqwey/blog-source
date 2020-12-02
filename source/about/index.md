---
layout: page
title: 关于
meta:
  header: []
  footer: []
sidebar: []
valine:
  placeholder: 有什么想对我说的呢？
---

{% btns circle center %}
{% cell Linhk1606, , https://cdn.jsdelivr.net/gh/Linhk1606/blog-cdn@0.0.6.5/img/avatar.webp %}
{% endbtns %}

![练习情况](https://luogu.vercel.app/api?id=106887)

* 本网站为纯粹的 OI Blog，无任何私人内容
* 快读模板

```cpp
#include <bits/stdc++.h>

using namespace std;

typedef long long ll;

namespace IO
{
    const int S = (1 << 20) + 5;
    char buf[S], *H, *T;
    inline char Get()
    {
        if (H == T)
            T = (H = buf) + fread(buf, 1, S, stdin);
        if (H == T)
            return -1;
        return *H++;
    }
    inline ll read()
    {
        ll x = 0;
        char c = Get();
        while (!isdigit(c))
            c = Get();
        while (isdigit(c))
            x = x * 10 + c - '0', c = Get();
        return x;
    }
    char obuf[S], *oS = obuf, *oT = oS + S - 1, c, qu[55];
    int qr;
    inline void flush()
    {
        fwrite(obuf, 1, oS - obuf, stdout);
        oS = obuf;
    }
    inline void putc(char x)
    {
        *oS++ = x;
        if (oS == oT)
            flush();
    }
    template <class I>
    inline void print(I x)
    {
        if (!x)
            putc('0');
        while (x)
            qu[++qr] = x % 10 + '0', x /= 10;
        while (qr)
            putc(qu[qr--]);
        putc('\n');
    }
} // namespace IO

using namespace IO;

int main()
{
  ll a = read(), b = read();
  print(a + b);
  flush();
  return 0;
}
```
