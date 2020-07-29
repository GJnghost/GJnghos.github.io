---
layout:     post
title:      『ACM-XCPC | Computational Geometry』是计算几何的板子
subtitle:   kkkkkkk自用的板子
date:       2020-07-24
author:     GJ
header-img: img/Texas_one.jpg
catalog: true
tags:
    - ACM-XCPC
    - Computational Geometry
---

## 点与向量

其实也就是用之前的一些头啦

```c++
const double eps = 1e-7;//eps用于控制精度
const double pi = acos(-1.0);//pi
struct Point {//点或向量
	double x, y;
	Point() {}
	Point(double x, double y) :x(x), y(y) {}
}P[maxn];

typedef Point Vector;
Vector operator + (Vector a, Vector b) { return Vector(a.x + b.x, a.y + b.y); }//向量加法
Vector operator - (Vector a, Vector b) { return Vector(a.x - b.x, a.y - b.y); }//向量减法
Vector operator * (Vector a, double p) { return Vector(a.x * p, a.y * p); }//向量数乘
Vector operator / (Vector a, double p) { return Vector(a.x / p, a.y / p); }//向量数除
int dcmp(double x) { if (fabs(x) < eps) return 0; else if (x > 0) return 1; return -1; }//精度三态函数(>0,<0,=0)
bool operator == (const Point& a, const Point& b) { return dcmp(a.x - b.x) == 0 && dcmp(a.y - b.y) == 0; }//向量相等
double Dot(Vector a, Vector b) { return a.x * b.x + a.y * b.y; }//内积//=0则垂直
double Cross(Vector a, Vector b) { return a.x * b.y - a.y * b.x; }//外积//四边形面积//=0则平行//<0则a顺时针转到b
Vector Rotate(Vector a, double rad) { return Vector(a.x * cos(rad) - a.y * sin(rad), a.x * sin(rad) + a.y * cos(rad)); }//逆时针旋转
double Length(Vector a) { return sqrt(Dot(a, a)); }//模
double Angle(Vector a, Vector b) { return acos(Dot(a, b) / Length(a) / Length(b)); }//夹角,弧度制//还不太顺手
double Distance(Point a, Point b) { return sqrt((a.x - b.x) * (a.x - b.x) + (a.y - b.y) * (a.y - b.y)); }//两点间距离
double Area(Point a, Point b, Point c) { return fabs(Cross(b - a, c - a) / 2); }//三角形面积
```

## 直线与线段

```c++
bool Intersect(Point A, Point B, Point C, Point D) {//线段相交（不包括端点）
	double t1 = Cross(C - A, D - A) * Cross(C - B, D - B);
	double t2 = Cross(A - C, B - C) * Cross(A - D, B - D);
	return dcmp(t1) < 0 && dcmp(t2) < 0;
}
bool StrictIntersect(Point A, Point B, Point C, Point D) { //线段相交（包括端点）
	return
		dcmp(max(A.x, B.x) - min(C.x, D.x)) >= 0
		&& dcmp(max(C.x, D.x) - min(A.x, B.x)) >= 0
		&& dcmp(max(A.y, B.y) - min(C.y, D.y)) >= 0
		&& dcmp(max(C.y, D.y) - min(A.y, B.y)) >= 0
		&& dcmp(Cross(C - A, D - A) * Cross(C - B, D - B)) <= 0
		&& dcmp(Cross(A - C, B - C) * Cross(A - D, B - D)) <= 0;
}
double DistanceToLine(Point A, Point M, Point N) {//点A到直线MN的距离,Error:MN=0
	return fabs(Cross(A - M, A - N) / Distance(M, N));
}
Point GetLineIntersection(Point P, Vector v, Point Q, Vector w) {//两直线的交点//预判Cross(v,w)≠0则有且仅有一个解
	Vector u = P - Q;
	double t = Cross(w, u) / Cross(v, w);
	return P + v * t;
}
```

## 多边形

```c++
//P[]为多边形的所有顶点，下标为0~n-1，n为多边形边数
bool InsidePolygon (Point A){ //判断点是否在凸多边形内（角度和判别法）
    double alpha = 0;
    for (int i = 0; i < n; i++) alpha += fabs(Angle(P[i] - A, P[(i + 1) % n] - A));
    return dcmp(alpha - 2 * pi) == 0;
}
double PolygonArea(){//求多边形面积（叉积和计算法）
    double sum = 0;
    Point O = Point(0, 0);
    for (int i = 0; i < n; i++) sum += Cross(P[i] - O, P[(i + 1) % n] - O);
    if (sum < 0)sum = -sum;
    return sum / 2;
}
```

### 凸包

#### Graham’s scan算法

n为所有点的个数，top为栈顶，P[]为所有点，下标为0 ~ n-1，result[]为凸包上的点，下标为0~top，包含凸包边上的点

**Error**:有重复点

```c++
bool cmp(Point A, Point B){
    double ans = Cross(A - P[0], B - P[0]);
    if (dcmp(ans) == 0)
        return dcmp(Distance(P[0], A) - Distance(P[0], B)) < 0;
    else
        return ans > 0;
}
void Graham(){//Graham凸包扫描算法
    for (int i = 1; i < n; i++)//寻找起点
        if (P[i].y < P[0].y || (dcmp(P[i].y - P[0].y) == 0 && P[i].x < P[0].x))
            swap(P[i], P[0]);
    sort(P + 1, P + n, cmp);//极角排序，中心为起点
    result[0] = P[0];
    result[1] = P[1];
    top = 1;
    for (int i = 2; i < n; i++){
        while (top >= 1 && Cross(result[top] - result[top - 1], P[i] - result[top - 1]) < 0) top--;
        result[++top] = P[i];
    }
}
```

#### Andrew's monotone chain 算法

上下凸包分开维护

```c++
inline bool pcmp(Point a, Point b) {
	if (dcmp(a.x - b.x) == 0)
		return a.y < b.y;
	return a.x < b.x;
}
void ConvexHull(vector <Point>& p, vector <Point>& h) {
	int n = p.size(), k = 0;
	h.assign(2 * n, {});
	sort(p.begin(), p.end(), pcmp);
	for (int i = 0; i < n; i++) {
		while (k >= 2 && dcmp(Cross(h[k - 1] - h[k - 2],p[i] - h[k - 2])) < 0) k--;
		h[k++] = p[i];
	}
	int t = k + 1;
	for (int i = n - 1; i > 0; i--) {
		while (k >= t && dcmp(Cross(h[k - 1] - h[k - 2],p[i - 1] - h[k - 2])) < 0) k--;
		h[k++] = p[i - 1];
	}
	h.resize(k - 1);
}
```

### 直线凸包

两道相关，来自计蒜客 //其实还没try

- [41306](https://nanti.jisuanke.com/t/41306)

- [41097](https://nanti.jisuanke.com/t/41097)

维护下凸包，对于每个x维护f(x)=k*x+b的最大值

```c++
template<typename var = long long, const int SIZE = 1000005, typename ldb = long double>
struct Hull {
	struct fx {
		var k, b;
		fx() {}
		fx(var k, var b) : k(k), b(b) {}
		var f(var x) { return k * x + b; }
	};
	int cnt;
	fx arr[SIZE];
	bool empty() { return cnt == 0; }
	void init() { cnt = 0; }
	void add(const fx& p) {arr[cnt++] = p;}
	void pop() {	cnt--;}
	bool chek(const fx& a, const fx& b, const fx& c) {
		ldb ab, ak, bb, bk, cb, ck;
		tie(ab, ak, bb, bk, cb, ck) = tie(a.b, a.k, b.b, b.k, c.b, c.k);
		return (ab - bb) / (bk - ak) > (ab - cb) / (ck - ak);
	}
	void insert(const fx& p) {///k从小到大插入
		if (cnt && arr[cnt - 1].k == p.k) {
			if (p.b <= arr[cnt - 1].b)return;
			else pop();
		}
		while (cnt >= 2 && chek(arr[cnt - 2], arr[cnt - 1], p))pop();
		add(p);
	}
	/*var query(var x) {///x从大到小查询       从小到大用队列
		while (cnt > 1 && arr[cnt - 2].f(x) > arr[cnt - 1].f(x))pop();;
		return arr[cnt - 1].f(x);
	}*/
	var query(var x) {///二分查询，x顺序任意
		int l = 0, r = cnt - 1;
		while (l < r) {
			int mid = (l + r) >> 1;
			if (arr[mid].f(x) >= arr[mid + 1].f(x))r = mid;
			else l = mid + 1;
		}
		return arr[l].f(x);
	}
};
```

```c++
// vector stack
template<typename var = long long, const int SIZE = 1000005, typename ldb = long double>
struct Hull {
	struct Line {
		var k, b;
		Line() {}
		Line(var k, var b) : k(k), b(b) {}
		var f(var x) { return k * x + b; }
	};
	int cnt;
	vector <Line> con;//
	bool empty() {return cnt == 0;}
	void init(const int& n) {
		con.clear();
		if (n > con.capacity())con.reserve(n);
		cnt = 0;
	}
	void add(const Line& p) {con.emplace_back(p);cnt++;}
	void pop() {cnt--;con.pop_back();}
	bool chek(const Line& a, const Line& b, const Line& c) {
		ldb ab, ak, bb, bk, cb, ck;
		tie(ab, ak, bb, bk, cb, ck) = tie(a.b, a.k, b.b, b.k, c.b, c.k);
		return (ab - bb) / (bk - ak) > (ab - cb) / (ck - ak);
	}
	void insert(const Line& p) {///k从小到大插入
		if (cnt && con[cnt - 1].k == p.k) {
			if (p.b <= con[cnt - 1].b)return;
			else pop();
		}
		while (cnt >= 2 && chek(con[cnt - 2], con[cnt - 1], p))pop();
		add(p);
	}
	var query(var x) {///二分查询，x顺序任意
		int l = 0, r = cnt - 1;
		while (l < r) {
			int mid = (l + r) >> 1;
			if (con[mid].f(x) >= con[mid + 1].f(x))r = mid;
			else l = mid + 1;
		}
		return con[l].f(x);
	}
};
Hull<> hull;
```

应该是我不配

## 圆

```c++
struct Circle{
	Point c;
	double r;
	Point point(double a){//基于圆心角求圆上一点坐标
		return Point(c.x + cos(a) * r, c.y + sin(a) * r);
	}
};
double Angle(Vector v1){
	if (v1.y >= 0)return Angle(v1, Vector(1.0, 0.0));
	else return 2 * pi - Angle(v1, Vector(1.0, 0.0));
}
int GetCC(Circle C1, Circle C2) {//求两圆交点
	double d = Length(C1.c - C2.c);
	if (dcmp(d) == 0){
		if (dcmp(C1.r - C2.r) == 0)return -1;//重合
		else return 0;
	}
	if (dcmp(C1.r + C2.r - d) < 0)return 0;
	if (dcmp(fabs(C1.r - C2.r) - d) > 0)return 0;
	double a = Angle(C2.c - C1.c);
	double da = acos((C1.r * C1.r + d * d - C2.r * C2.r) / (2 * C1.r * d));
	Point p1 = C1.point(a - da), p2 = C1.point(a + da);
	if (p1 == p2)return 1;
	else return 2;
}
```

### 不共线三点求圆心（外心）

其实自己现推问题不大，就是可能会慢一点？//再考虑到学长可能甚至懒得

```c++
template<typename tp>
inline tp pow2(const tp& x) {return x * x;}
inline Point circumcenter(Point p1, Point p2, Point p3) {
	double a = p1.x - p2.x;
	double b = p1.y - p2.y;
	double c = p1.x - p3.x;
	double d = p1.y - p3.y;
	double e = (pow2(p1.x) - pow2(p2.x) + pow2(p1.y) - pow2(p2.y)) / 2;
	double f = (pow2(p1.x) - pow2(p3.x) + pow2(p1.y) - pow2(p3.y)) / 2;
	return Point((d * e - b * f) / (a * d - b * c), (a * f - c * e) / (a * d - b * c));
}
```

### 最小圆覆盖

一道题，来自[洛谷P1742](https://www.luogu.com.cn/problem/P1742)

```c++
void MinCircleCover(vector <Point>& p, Circle& res) {
	int n = p.size();
	random_shuffle(p.begin(), p.end());// avoid *sqrt* too much killing your precision.
	for (int i = 0; i < n; i++) {
		if (Distance2(p[i], res.c) <= res.r) continue;
		res.c = p[i];
		res.r = 0;
		for (int j = 0; j < i; j++) {
			if (Distance2(p[j], res.c) <= res.r)continue;
			res.c = (p[i] + p[j]) / 2;
			res.r = Distance2(p[j], res.c);
			for (int k = 0; k < j; k++) {
				if (Distance2(p[k], res.c) <= res.r)continue;
				res.c = circumcenter(p[i], p[j], p[k]);
				res.r = Distance2(p[k], res.c);
			}
		}
	}
	res.r = sqrt(res.r);
}
```

~~哈！基本的应该够用了，之后的之后再补~~

## 几何公式

**三角形**

1. 半周长` P=(a+b+c)/2`

2. 面积 `S=aHa/2=absin(C)/2=sqrt(P(P-a)(P-b)(P-c))`

3. 中线 `Ma=sqrt(2(b^2+c^2)-a^2)/2=sqrt(b^2+c^2+2bccos(A))/2`

4. 角平分线 `Ta=sqrt(bc((b+c)^2-a^2))/(b+c)=2bccos(A/2)/(b+c)`

5. 高线 `Ha=bsin(C)=csin(B)=sqrt(b^2-((a^2+b^2-c^2)/(2a))^2)`

6. 内切圆半径 `r=S/P=asin(B/2)sin(C/2)/sin((B+C)/2)`

​        =`4Rsin(A/2)sin(B/2)sin(C/2)=sqrt((P-a)(P-b)(P-c)/P)`

​        =`Ptan(A/2)tan(B/2)tan(C/2)`

7. 外接圆半径 `R=abc/(4S)=a/(2sin(A))=b/(2sin(B))=c/(2sin(C))`

**四边形**

D1,D2为对角线,M对角线中点连线,A为对角线夹角

1. `a^2+b^2+c^2+d^2=D1^2+D2^2+4M^2`

2. `S=D1D2sin(A)/2`

(以下对圆的内接四边形)

3. `ac+bd=D1D2`

4. `S=sqrt((P-a)(P-b)(P-c)(P-d))` , P为半周长

**正n边形**

R为外接圆半径,r为内切圆半径

1. 中心角 `A=2PI/n`

2. 内角 `C=(n-2)PI/n`

3. 边长 `a=2sqrt(R^2-r^2)=2Rsin(A/2)=2rtan(A/2)`

4. 面积 `S=nar/2=nr^2tan(A/2)=nR^2sin(A)/2=na^2/(4tan(A/2))`

**圆**

1. 弧长 `l=rA`

2. 弦长 `a=2sqrt(2hr-h^2)=2rsin(A/2)`

3. 弓形高 `h=r-sqrt(r^2-a^2/4)=r(1-cos(A/2))=atan(A/4)/2`

4. 扇形面积 `S1=rl/2=r^2A/2`

5. 弓形面积 `S2=(rl-a(r-h))/2=r^2(A-sin(A))/2`

**棱柱**

1. 体积 `V=Ah`,A为底面积,h为高

2. 侧面积 `S=lp` ,l为棱长,p为直截面周长

3. 全面积 `T=S+2A`

**棱锥**

1. 体积 `V=Ah/3`,A为底面积,h为高

(以下对正棱锥)

2. 侧面积 `S=lp/2`,l为斜高,p为底面周长

3. 全面积 `T=S+A`

**棱台**

1. 体积 `V=(A1+A2+sqrt(A1A2))h/3`,A1.A2为上下底面积,h为高

(以下为正棱台)

2. 侧面积 `S=(p1+p2)l/2`,p1.p2为上下底面周长,l为斜高

3. 全面积 `T=S+A1+A2`

**圆柱**

1. 侧面积 `S=2PIrh`

2. 全面积 `T=2PIr(h+r)`

3. 体积 `V=PIr^2h`

**圆锥**

1. 母线 `l=sqrt(h^2+r^2)`

2. 侧面积 `S=PIrl`

3. 全面积 ` T=PIr(l+r)`

4. 体积 `V=PIr^2h/3`

**圆台**

1. 母线` l=sqrt(h^2+(r1-r2)^2)`

2. 侧面积 `S=PI(r1+r2)l`

3. 全面积 `T=PIr1(l+r1)+PIr2(l+r2)`

4. 体积 `V=PI(r1^2+r2^2+r1r2)h/3`

**球**

1. 全面积 `T=4PIr^2`

2. 体积 `V=4PIr^3/3`

**球台**

1. 侧面积 `S=2PIrh`

2. 全面积 `T=PI(2rh+r1^2+r2^2)`

3. 体积 `V=PIh(3(r1^2+r2^2)+h^2)/6`

**球扇形**

1. 全面积 `T=PIr(2h+r0)`,h为球冠高,r0为球冠底面半径

2. 体积 `V=2PIr^2h/3`

## #.附录

### 真题

> 出过的题基本不会再出现，但是可以看看自己板子怎么样。

- [2019 ICPC 沈阳站 E.Capture Stars](https://www.cnblogs.com/AEMShana/p/12452762.html) （没有开放提交平台）
- [2019 ICPC 南京站 K.Triangle](https://nanti.jisuanke.com/t/42405) [题解](https://www.cnblogs.com/wulitaotao/p/11755964.html) 铜牌题
- [2019 ICPC 西安站邀请赛 C. Angel's Journey](https://blog.csdn.net/qq_41835683/article/details/90577692)
- [2019 ICPC 上海站 I](https://ac.nowcoder.com/acm/contest/4370/I)  [一个题解](https://www.cnblogs.com/xiaobuxie/p/12485717.html)
- [2019 CCPC 秦皇岛 A题（计算几何）](https://www.cnblogs.com/rentu/p/11642537.html)
- [2018 ICPC 南京站 D.Country Meow](http://www.baidu.com/link?url=pCccIM_daajkd8wfqGEZESGajRSTRpq-M0MsWfwoHTyNIdoZjhkZBT7GWnBxZXqFnZ6XCUAoqWTIkHpoR2yWRq)
- [2018 ICPC 沈阳站 L Machining Disc Rotors](https://blog.csdn.net/qq_40791842/article/details/100907900)
- [2017 ICPC 北京站 G.Liaoning Ship's Voyage](https://blog.csdn.net/qq_40791842/article/details/101486595)
- [2015 ICPC 上海站 I 计算几何+组合计数](https://blog.csdn.net/foreyes_1001/article/details/52228058)

### 清单

> 一些可以准备的有意思的主题。

- [ ] 最远曼哈顿距离
- [ ] 包卡壳旋转求出所有对踵点、最远点对
- [ ] 最近点对
- [ ] 最近圆对
- [ ] 费马点（所有点到某坐标距离和最短）
- [ ] 求两个圆的交点
- [ ] 凸包+旋转卡壳求平面面积最大三角
- [ ] Pick定理
- [ ] 求多边形面积和重心
- [ ] 判断一个简单多边形是否有核
- [ ] 模拟退火
- [ ] 六边形坐标系
- [ ] 用一个给定半径的圆覆盖最多的点
- [ ] 不等大的圆的圆弧表示
- [ ] 矩形面积并
- [ ] 矩形的周长并
- [ ] 求两个圆的面积交
- [ ] 圆的反演变换
