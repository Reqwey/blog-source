---
title: Bitset 的用法
date: "2020-02-06 00:00:00"
body: [article, comments]
cover: false

valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
tags:
 - bitset
categories:
 - [OI, Grammar]
description: "C++ STL Bitset 用法记录"
---

### 简介

> 相当于一种特别长的二进制数, 可以支持各种二进制位运算等

### 构造函数

常见有四种构造形式

```cpp
bitset<4> bitset1;　　//无参构造，长度为４，默认每一位为0

bitset<8> bitset2(12);　　//长度为８，二进制保存，前面用0补充

string s = "100101";
bitset<10> bitset3(s);　　//长度为10，前面用0补充

char s2[] = "10101";
bitset<13> bitset4(s2);　　//长度为13，前面用0补充

cout << bitset1 << endl;　　//0000
cout << bitset2 << endl;　　//00001100
cout << bitset3 << endl;　　//0000100101
cout << bitset4 << endl;　　//0000000010101
```

{% noteblock warning %}
**注意**

当赋值时bitset的长度**大于**数(或字符串)的长度时, 前面用`0`补充

当赋值时bitset的长度**小于**数(或字符串)的长度时, 数截取后面几位(从$2^0$开始), 字符串截取前面几位(从`s[0]`开始)
{% endnoteblock %}

### 操作符位运算

> bitset 支持像普通整形数一样进行位计算并赋值

```cpp
bitset<4> foo (string("1001"));
bitset<4> bar (string("0011"));

cout << (foo^=bar) << endl;       // 1010 (foo对bar按位异或后赋值给foo)
cout << (foo&=bar) << endl;       // 0010 (按位与后赋值给foo)
cout << (foo|=bar) << endl;       // 0011 (按位或后赋值给foo)

cout << (foo<<=2) << endl;        // 1100 (左移２位，低位补０，有自身赋值)
cout << (foo>>=1) << endl;        // 0110 (右移１位，高位补０，有自身赋值)

cout << (~bar) << endl;           // 1100 (按位取反)
cout << (bar<<1) << endl;         // 0110 (左移，不赋值)
cout << (bar>>1) << endl;         // 0001 (右移，不赋值)

cout << (foo==bar) << endl;       // false (0110==0011为false)
cout << (foo!=bar) << endl;       // true  (0110!=0011为true)

cout << (foo&bar) << endl;        // 0010 (按位与，不赋值)
cout << (foo|bar) << endl;        // 0111 (按位或，不赋值)
cout << (foo^bar) << endl;        // 0101 (按位异或，不赋值)
```

### 内置函数

> 对于一个叫做bit的bitset：

| 代码              | 解释                                                       |
| ----------------- | ---------------------------------------------------------- |
| `bit.size()`      | 返回大小（位数）                                           |
| `bit.count()`     | 返回`1`的个数                                              |
| `bit.any()`       | 返回是否有`1`                                              |
| `bit.none()`      | 返回是否没有`1`                                            |
| `bit.set()`       | 全都变成`1`                                                |
| `bit.set(p)`      | 将第`p + 1`位变成`1`（bitset是从第`0`位开始的！）          |
| `bit.set(p, x)`   | 将第`p + 1`位变成`x`                                       |
| `bit.reset()`     | 全都变成`0`                                                |
| `bit.reset(p)`    | 将第`p + 1`位变成`0`                                       |
| `bit.flip()`      | 全都取反                                                   |
| `bit.flip(p)`     | 将第`p + 1`位取反                                          |
| `bit.to_ulong()`  | 返回它转换为`unsigned long`的结果，如果超出范围则报错      |
| `bit.to_ullong()` | 返回它转换为`unsigned long long`的结果，如果超出范围则报错 |
| `bit.to_string()` | 返回它转换为`string`的结果                                 |



### 头文件

```cpp
#include <bitset>
```
