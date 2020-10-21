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
#include <cstdio>
#include <iostream>
#include <algorithm>

using namespace std;

typedef unsigned long long ull;

inline ull read() {
    ull s = 0, f = 1; char ch;
    while (!isdigit(ch = getchar())) (ch == '-') && (f = -f);
    for (s = ch ^ 48; isdigit(ch = getchar()); s = (s << 1) + (s << 3) + (ch ^ 48));
    return s * f;
}

int main()
{
  	ull a = read();
    return 0;
}
```