---
layout:     post
title:      『ACM-XCPC | Data Structure』树状数组
subtitle:   太多了呜呜呜就分块啦
date:       2020-07-29
author:     GJ
header-img: img/Others_four.jpg
catalog: true
tags:
    - ACM-XCPC
    - Data Structure
---


- **注意树状数组无法处理0的情况**

### 一维树状数组

- 注意初始化d数组
- **复杂度：O(logN)**
- 树状数组并非支持所有的区间更新,例如区间赋值等

![](https://GJnghost.github.io/img/20200729_1.png)

```c++
const int maxn = 1e5 + 10;
int d[maxn], n;
int lowbit(int x) { return x & (-x); }  //lowbit无法处理0的情况，因为它的结果也是0，最终为一个死循环
int query(int x) { int res = 0; while (x) { res += d[x]; x -= lowbit(x); }return res; } //区间查询 O(logN)
void add(int x, int v) { while (x <= n) { d[x] += v; x += lowbit(x); } }//单点更新 O(logN)对a[x]加上v 
```

### 二维树状数组

> 板子来自[LUOGU P4514 上帝造题的七分钟](https://www.luogu.com.cn/problem/P4514)
>
> 保证所有数据范围在int范围内
>
> 然后……开**ll**会**MLE**

- 差分思想

```c++
const int maxn = 3e3 + 10;
int n, m, ans, d2[4][maxn][maxn];
int lowbit(int x) { return x & (-x); }  //lowbit无法处理0的情况，因为它的结果也是0，最终为一个死循环

void add2(int x, int y, int delta) {
	if (x < 1 || n < x || y < 1 || m < y) return;
	for(int i=x;i<=n;i+=lowbit(i))
		for (int j = y; j <= m; j+=lowbit(j)) {
			d2[0][i][j] += delta;
			d2[1][i][j] += delta * y;
			d2[2][i][j] += delta * x;
			d2[3][i][j] += delta * x * y;
		}
}

int query2(int x, int y) {
	int res = 0;
	for (int i = x; i ;i-=lowbit(i))
		for (int j = y; j; j-=lowbit(j))
			res = res + (x + 1) * (y + 1)* d2[0][i][j] - (x + 1) * d2[1][i][j] - (y + 1) * d2[2][i][j] + d2[3][i][j];
	return res;
}

int main(){
	string s;
	while (cin >> s) {
		int a, b, c, d,delta;
		if (s[0] == 'X') cin >> n >> m;
		else if (s[0] == 'L') {
			cin >> a >> b >> c >> d >> delta;
			add2(a, b, delta);
			add2(a, d + 1, -delta);
			add2(c + 1, b, -delta);
			add2(c+1, d+1, delta);
		}
		else {
			cin >> a >> b >> c >> d;
			cout << query2(a-1, b-1) - query2(a-1, d) - query2(c, b-1) + query2(c, d) << "\n";
		}
	}
	
	return 0;
}
```

### N维树状数组

- 更新**$$2^n$$**个点
- 就再添上for就行啦，n重for