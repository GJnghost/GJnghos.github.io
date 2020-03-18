---
layout:     post
title:      【ACM-XCPC|Number theory】拓展欧几里得
subtitle:   前路漫漫的数论之路🦌
date:       2020-03-18
author:     GJ
header-img: img/Others_five.jpg
catalog: true
tags:
    - ACM-XCPC
    - Number theory
---

## 欧几里得算法

扩展欧几里得的前置技能，即**辗转相除法**求gcd

**相关定理**:	gcd(a, b) = gcd(b, a) = gcd(-a, b) = `gcd(|a|, |b|)` `//绝对值不太会敲emm`

**相关公式**:	gcd(a, b) = gcd(b, a%b)

```
ll gcd(ll a,ll b){
    return b?gcd(b,a%b):a;
}
```

---

## 扩展欧几里得

在已知a, b, d的情况下求解一组x，y，使它们满足贝祖等式： ax+by = gcd(a, b) =d（解一定存在，根据数论中的相关定理）。扩展欧几里德常用在求解模线性方程及方程组中。

##### 求解|证明：

​	① 显然 a = gcd(a, b), b = 0 (递归至边界)时，x = 1，y = 0 ;	`//[A]任取问题也不大//为啥y=0，理论上啥都行趴`

​	② 设ax<sub>1</sub> + by<sub>1</sub> = gcd(a, b) ;

​			 bx<sub>2</sub> + (a % b)y<sub>2</sub> = gcd(b, a % b) ;

​		 根据欧几里得原理有 gcd(a, b) = gcd(b, a%b) ;

​		 则: ax<sub>1</sub> + by<sub>1</sub> = bx<sub>2</sub> + (a % b)y<sub>2</sub> ;

​		 即: ax<sub>1</sub> + by<sub>1</sub> = bx<sub>2</sub> + (a - ⌊a / b⌋ * b)y<sub>2</sub> = ay<sub>2 </sub>+ bx<sub>2</sub> - ⌊a / b⌋ * by<sub>2</sub> ;

​		 因此: ax<sub>1</sub> + by<sub>1</sub> = = ay<sub>2 </sub>+ bx<sub>2</sub> - ⌊a / b⌋ * by<sub>2</sub> ;

​		 得: x<sub>1</sub> = y<sub>2</sub> ; y<sub>1</sub> = x<sub>2</sub> - ⌊a / b⌋ * y<sub>2</sub> ;

##### Code：

```JavaScript
ll ex_gcd(ll a,ll b,ll& x,ll& y){
    if(b==0){
        x=1;
        y=0;
        return a;
    }
    ll r=ex_gcd(b,a%b,x,y);
    ll tmp=x;
    x=y;
    y=tmp-a/b*y;
	return r;	//突然不太懂这边返回干啥，应该是有用的？
}
```

```c++
ll ex_gcd(ll a,ll b,ll& d,ll& x,ll& y){
    if(!b) { d=a; x=1; y=0; }
    else { gcd(b,a%b,d,y,x); y-=(a/b)*x; }
}
```

---

## 应用

#### 求解逆元

逆元，即 a<sup>-1</sup> 

a在模数p意义下**逆元存在的充要条件：gcd(a, p) = 1 **，即a与p互质

##### 求解|证明：

① p为质数时，费马小定理：a<sup>p-1</sup> ≡ 1 (mod p) ,求 ax ≡ 1 (mod p) ,得 x = a<sup>p-2</sup> 

② p为合数时，扩展欧几里得：求 ax ≡ 1 (mod p)，即求 ax + py =1 一组特解

##### Samples：

[[Luogu] P1082 同余方程组(@Zimba_)](https://www.luogu.com.cn/problem/P1082)

裸题

```c++
#include<bits/stdc++.h>

#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);

using namespace std;

typedef long long ll;

ll ex_gcd(ll a, ll b, ll& x, ll& y) {
    if (b == 0) {
        x = 1;
        y = 0;    //最后一个等式不受最后一个y的影响，任取问题不大
        return a;
    }
    ll r = ex_gcd(b, a % b, x, y);
    ll tmp = x;
    x = y;
    y = tmp - a / b * y;
    return r;
}

ll solve(ll a, ll p) {
    ll x, y;    //目前方程的解
    ll tmp = ex_gcd(a, p, x, y);
    return (x % p + p) % p;    //PAY ATTENTION 处理负数
}

int main() {
    ll a, p;
    cin >> a >> p;
    cout << solve(a, p) << "\n";
    return 0;
}
```

```c++
#include<bits/stdc++.h>

#define O_O ios::sync_with_stdio(false);cin.tie(0);cout.tie(0);

using namespace std;

typedef long long ll;
ll x, y;

 void ex_gcd(ll a, ll b,ll& x,ll& y) {
     if (!b) { x = 1; y = 0; return; }
     else { ex_gcd(b, a % b, y, x); y -= a / b * x; }
}

int main() {
    O_O;
    ll a, p;
    cin >> a >> p;
    ex_gcd(a, p, x, y);
    cout << (x%p+p)%p << "\n";
    return 0;
}
```

