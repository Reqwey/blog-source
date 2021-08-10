---
title: 算法笔记 - 并查集按秩合并
date: "2021-08-10 00:00:00"
body: [article, comments]
cover: false
valine:
  placeholder: 有什么感想? 发射犇犇
mathjax: true
tags:
 - 并查集
 - 按秩合并
categories:
 - [OI, Algorithm]
description: "让你的并查集快到飞起~"
---

> 如何让你的并查集快到飞起？
> 
> ——使用按秩合并！

## 入门
我们都知道，并查集的路径压缩大大加速了操作的时间复杂度，但是如果出题人足够毒瘤，仍然可以把你卡掉。

比如说你在`1`号点上连了`2`、`3`、`4`、`5`, 这时候进来个`6`，您觉得应该将`1`连到`6`上更优呢？还是反过来更优呢？

基于减少复杂度的贪心思想，我们当然希望能够把一棵**较小的**并查集并入**较大的**一棵当中。

那我们就多维护一个`siz[root]`，表示当前这棵以`root`为根的并查集的**大小**或**深度**。

这样，我们以`siz`作为关键字进行判断谁应该合并到谁的父亲上。

## 更进一步

> 开两个数组好累啊 QwQ
> 
> 那好，我们可以只开一个！

通过观察，我们发现这棵并查集，它的根节点的父亲`fa[root]`是没有用的，他唯一的功能是判断**到头了没有**。

那我们可以令`fa[root] = siz[root]`。

当然，也许会问，如果是这样会不会造成无法判断根，数组越界？

说的好！

那我们可以把他改成这样：`fa[root] = -siz[root]`。

一旦发现`fa[x] < 0`，就立刻发现不对劲！节点编号不可能是负数！那一定是到头啦，于是我们愉快的把`x`揪了出来，他就是根节点！

看起来细节还蛮多的嘛！

可不是嘛！代码写起来细节更多！尤其是如果写成非递归的形式，那么你可能要提前判一下`fa[fa[x]]`是否是负数！小心跳过头了哦！

## 实战！

{% folding blue, 查看题目 %}

![题目](https://i.loli.net/2021/08/10/WBVSfQIZTMj1AHK.png)

{% endfolding %}

### 以子树大小为关键字
#### 在 `main()` 中

```cpp
read(n), read(m);
for (int i = 1; i <= n; i++) fa[i] = -1;
for (int i = 1; i <= m; i++) {
	int z, x, y;
	read(z), read(x), read(y);
	if (z == 1) {
		int fx = f(x), fy = f(y);
		if (fx != fy) {
			if (fa[fx] < fa[fy])
				fa[fx] += fa[fy], fa[fy] = fx;
			else {
				fa[fy] += fa[fx], fa[fx] = fy;
			}
		}
	} else {
		int fx = f(x), fy = f(y);
		puts(fx == fy ? "Y" : "N");
	}
}
```

#### `f()` 函数

```cpp
const int N = 1e4 + 5;
int fa[N];

IL int f(int x) {
    while (fa[x] > 0 && fa[fa[x]] > 0) x = fa[x] = fa[fa[x]];
    return fa[x] < 0 ? x : fa[x];
}
```

### 以深度为关键字
#### 在 `main()` 中

```cpp
read(n), read(m);
for (int i = 1; i <= n; i++) fa[i] = -1;
for (int i = 1; i <= m; i++) {
	int z, x, y;
	read(z), read(x), read(y);
	if (z == 1) {
		int fx = f(x), fy = f(y);
		if (fx != fy) {
			if (fa[fx] < fa[fy])
				fa[fy] = fx;
			else {
				if (fa[fx] == fa[fy])
					fa[fy]--;
				fa[fx] = fy;
			}
		}
	} else {
		int fx = f(x), fy = f(y);
		puts(fx == fy ? "Y" : "N");
	}
}
```

#### `f()`函数

> 与第一种情况的相同