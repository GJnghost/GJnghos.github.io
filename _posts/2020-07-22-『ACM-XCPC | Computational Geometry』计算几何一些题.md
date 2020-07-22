---
layout:     post
title:      『ACM-XCPC | Computational Geometry』计算几何一些题
subtitle:   乱七八糟的题什么的
date:       2020-07-22
author:     GJ
header-img: img/Texas_one.jpg
catalog: true
tags:
    - ACM-XCPC
    - Computational Geometry
---



## Power Transmission  (Hard Edition)

 [CF-1163C2](https://vjudge.net/problem/CodeForces-1163C2)

 **Description**

This problem is same as the next one, but has smaller constraints.

It was a Sunday morning when the three friends Selena, Shiro and Katie decided to have a trip to the nearby power station (do not try this at home). After arriving at the power station, the cats got impressed with a large power transmission system consisting of many chimneys, electric poles, and wires. Since they are cats, they found those things gigantic.

At the entrance of the station, there is a map describing the complicated wiring system. Selena is the best at math among three friends. He decided to draw the map on the Cartesian plane. Each pole is now a point at some coordinates **(xi,yi)**. Since every pole is different, all of the points representing these poles are distinct. Also, every two poles are connected with each other by wires. A wire is a straight line on the plane infinite in both directions. If there are more than two poles lying on the same line, they are connected by a single common wire.

Selena thinks, that whenever two different electric wires intersect, they may interfere with each other and cause damage. So he wonders, how many pairs are intersecting? Could you help him with this problem?

**Input**

>  The first line contains a single integer **n (2≤n≤1000)** — the number of electric poles.
>
> Each of the following n lines contains two integers **xi, yi (−10<sup>4</sup>≤xi,yi≤10<sup>4</sup>)**  — the coordinates of the poles.
>
> It is guaranteed that all of these **n** points are distinct.

**Output**

> Print a single integer — the number of pairs of wires that are intersecting.

**Sample Input 1**

```
4
0 0
1 1
0 3
1 2
```

**Sample Output 1**

```
14
```

**Sample Input 2**

```
4
0 0
0 2
0 4
2 0
```

**Sample Output 2**

```
6
```

**Sample Input 3**

```
3
-1 -1
1 0
3 1
```

**Sample Output 3**

```
0
```

**Solution**

大意：给n个点，求两两相交的直线的对数

- 两两构造线 
- 线去重
- 统计交点

map+set NB！！！（破音//难过的点在于本来以为是到很好玩的计算几何，结果是道有点好玩的数据结构[卒]

**Code**

```c++
map<pair<ll, ll>, set<ll>>mp;
ll gcd(ll a, ll b) { return a == 0 ? b : gcd(b % a, a); }	//最大公约数

int main() {
	ll n, res= 0,ans=0; cin >> n;
	for (int i = 1; i <= n; i++) cin >> P[i].x >> P[i].y;
	for (int i = 1; i <= n; i++) {
		for (int j = i + 1; j <= n; j++) {
			ll a = P[j].y - P[i].y;
			ll b = P[j].x - P[i].x;
			ll c = P[i].x * P[j].y - P[i].y * P[j].x;
			ll g = gcd(a, b);
			a /= g, b /= g,c/=g;
			if (a < 0 || (a == 0 && b < 0)) a *= -1, b *= -1,c*=-1;
			if (mp[{a, b}].find(c) == mp[{a,b}].end()) {
				res++;
				mp[{a, b}].insert(c);
				ans += res - mp[{a, b}].size();
			}
		}
	}
	cout << ans << "\n";
	return 0;
}
```

## Triangle

[2019ICPC南京站K]([https://vjudge.net/problem/%E8%AE%A1%E8%92%9C%E5%AE%A2-42405](https://vjudge.net/problem/计蒜客-42405))

**Description**

One day, ABC and DD found a triangular wheat field, and they decide to cut it into two pieces with the same area with a segment. Because of various reasons, one of the endpoints of the division line is fixed at **(px,py)**. Now you are asked to find the other endpoint of the segment.

If the given endpoint does not lie on the boundary of the wheat field, the problem should be regarded as invalid. The other endpoint required should also locate on the boundary.

**Input**

> The input provides several test cases and the first line of the input contains a single integer **T (1≤T≤1000000)** indicati>ng the number of cases.
>
> For each test case, eight integers  **x<sub>1</sub>,y<sub>1</sub>,x<sub>2</sub>,y<sub>2</sub>,x<sub>3</sub>,y<sub>3</sub>,p<sub>x</sub>** and  **p<sub>y</sub>** are given in a line, where **(x1,y1),(x2,y2)** and **(x3,y3)** describe the coordinates of vertices for the given triangle wheat field which are guaranteed to be not colinear, and **(p<sub>x</sub>,p<sub>y</sub>)** is the given endpont of the segment. All coordinates are integers in the range **[0,100000]**.

**Output**

> For each test case, output the coordinate of the other endpoint of the division segment, or output the number `-1` if the problem, in this case, is invalid.
>
> Formally, if your answer is **a** and the jury's answer is **b**, then your answer will be considered correct if and only if `$\frac{|a−b|}{max(1,|b|)}≤10^{−6}$`.

**Sample Input**

```
2
0 0 1 1 1 0 1 0
0 0 1 1 1 0 2 0
```

**Sample Output**

```
0.500000000000 0.500000000000
-1
```

**Solution**

大意：给一个三角形的三个顶点和一个位于三角形边上的坐标，求与当前点连线能将三角形面积一分为二的在三角形上的点的坐标

- 预判顶点//想了想中点好像不用预判
- 确定当前点所在的边，通过相似三角形计算坐标

注意点：并非取长高所在边。if elseif elseif else 

**Code**

```c++
using namespace std;
typedef long long ll;
const int maxn = 2e3;
//计算几何
const double eps = 1e-7;//eps用于控制精度
const double pi = acos(-1.0);//pi
struct Point {//点或向量
	double x, y;
	Point() {}
	Point(double x, double y) :x(x), y(y) {}
}P[maxn];
//向量
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

//直线与线段
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
//圆

int main() {
	O_O;
	int n; cin >> n;
	for (int i = 1; i <= n; i++) {
		for (int j = 1; j <= 3; j++) cin >> P[j].x >> P[j].y;
		double area = Area(P[1], P[2], P[3]);
		double x, y; cin >> x >> y;
		if (x == P[1].x && y == P[1].y) { cout << fixed << setprecision(10) << (P[2].x + P[3].x) / 2.0 << " " << (P[2].y + P[3].y) / 2.0 << "\n"; continue; }
		if (x == P[2].x && y == P[2].y) { cout << fixed << setprecision(10) << (P[1].x + P[3].x) / 2.0 << " " << (P[1].y + P[3].y) / 2.0 << "\n"; continue; }
		if (x == P[3].x && y == P[3].y) { cout << fixed << setprecision(10) << (P[1].x + P[2].x) / 2.0 << " " << (P[1].y + P[2].y) / 2.0 << "\n"; continue; }
		if (dcmp(x - (P[1].x + P[2].x) / 2.0) == 0 && dcmp(y - (P[1].y + P[2].y) / 2.0) == 0) { cout << fixed << setprecision(10) << P[3].x << " " << P[3].y << "\n"; continue; }
		if (dcmp(x - (P[3].x + P[2].x) / 2.0) == 0 && dcmp(y - (P[3].y + P[2].y) / 2.0) == 0) { cout << fixed << setprecision(10) << P[1].x << " " << P[1].y << "\n"; continue; }
		if (dcmp(x - (P[1].x + P[3].x) / 2.0) == 0 && dcmp(y - (P[1].y + P[3].y) / 2.0) == 0) { cout << fixed << setprecision(10) << P[2].x << " " << P[2].y << "\n"; continue; }
		//在12上
		if (dcmp((x - P[1].x) * (P[1].y - P[2].y) - (y - P[1].y) * (P[1].x - P[2].x)) == 0) {
			if (P[1].x + P[1].y > P[2].x + P[2].y && ((x + y > P[1].x + P[1].y) || (x + y < P[2].x + P[2].y))) { cout << "-1\n"; continue; }
			if (P[1].x + P[1].y < P[2].x + P[2].y && ((x + y > P[2].x + P[2].y) || (x + y < P[1].x + P[1].y))) { cout << "-1\n"; continue; }
			double h1 = DistanceToLine({ x,y }, P[1], P[3]);
			double h2 = DistanceToLine({ x,y }, P[2], P[3]);
			if (h1 > h2) {
				double d = area / h1;
				double bd = Distance(P[1], P[3]);
				if (d > bd) { 
					d = area / h2;
					bd = Distance(P[2], P[3]);
					if (d > bd) { cout << "-1\n"; continue; }
					double yc = d * (P[3].y - P[2].y) / bd + P[2].y;
					double xc = d * (P[3].x - P[2].x) / bd + P[2].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[3].y - P[1].y) / bd + P[1].y;
				double xc = d * (P[3].x - P[1].x) / bd + P[1].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
			else {
				double d = area / h2;
				double bd = Distance(P[2], P[3]);
				if (d > bd) {
					double d = area / h1;
					double bd = Distance(P[1], P[3]);
					double yc = d * (P[3].y - P[1].y) / bd + P[1].y;
					double xc = d * (P[3].x - P[1].x) / bd + P[1].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[3].y - P[2].y) / bd + P[2].y;
				double xc = d * (P[3].x - P[2].x) / bd + P[2].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
		}
		//在23上
		else if (dcmp((x - P[3].x) * (P[3].y - P[2].y) - (y - P[3].y) * (P[3].x - P[2].x)) == 0) {
			if (P[2].x + P[2].y > P[3].x + P[3].y && ((x + y > P[2].x + P[2].y) || (x + y < P[3].x + P[3].y))) { cout << "-1\n"; continue; }
			if (P[2].x + P[2].y < P[3].x + P[3].y && ((x + y > P[3].x + P[3].y) || (x + y < P[2].x + P[2].y))) { cout << "-1\n"; continue; }
			double h1 = DistanceToLine({ x,y }, P[3], P[1]);
			double h2 = DistanceToLine({ x,y }, P[2], P[1]);
			if (h1 > h2) {
				double d = area / h1;
				double bd = Distance(P[1], P[3]);
				if (d > bd) { 
					double d = area / h2;
					double bd = Distance(P[2], P[1]);
					if (d > bd) { cout << "-1\n"; continue; }
					double yc = d * (P[1].y - P[2].y) / bd + P[2].y;
					double xc = d * (P[1].x - P[2].x) / bd + P[2].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[1].y - P[3].y) / bd + P[3].y;
				double xc = d * (P[1].x - P[3].x) / bd + P[3].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
			else {
				double d = area / h2;
				double bd = Distance(P[2], P[1]);
				if (d > bd) {
					double d = area / h1;
					double bd = Distance(P[1], P[3]);
					if (d > bd) {cout << "-1\n"; continue; }
					double yc = d * (P[1].y - P[3].y) / bd + P[3].y;
					double xc = d * (P[1].x - P[3].x) / bd + P[3].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[1].y - P[2].y) / bd + P[2].y;
				double xc = d * (P[1].x - P[2].x) / bd + P[2].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
		}
		//在13上
		else  if (dcmp((x - P[3].x) * (P[3].y - P[1].y) - (y - P[3].y) * (P[3].x - P[1].x)) == 0) {
			if (P[1].x + P[1].y > P[3].x + P[3].y && ((x + y > P[1].x + P[1].y) || (x + y < P[3].x + P[3].y))) { cout << "-1\n"; continue; }
			if (P[1].x + P[1].y < P[3].x + P[3].y && ((x + y > P[3].x + P[3].y) || (x + y < P[1].x + P[1].y))) { cout << "-1\n"; continue; }
			double h1 = DistanceToLine({ x,y }, P[1], P[2]);
			double h2 = DistanceToLine({ x,y }, P[3], P[2]);
			if (h1 > h2) {
				double d = area / h1;
				double bd = Distance(P[1], P[2]);
				if (d > bd) { 
					double d = area / h2;
					double bd = Distance(P[2], P[3]);
					if (d > bd) { cout << "-1\n"; continue; }
					double yc = d * (P[2].y - P[3].y) / bd + P[3].y;
					double xc = d * (P[2].x - P[3].x) / bd + P[3].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[2].y - P[1].y) / bd + P[1].y;
				double xc = d * (P[2].x - P[1].x) / bd + P[1].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
			else {
				double d = area / h2;
				double bd = Distance(P[2], P[3]);
				if (d > bd) { 
					double d = area / h1;
					double bd = Distance(P[1], P[2]);
					if (d > bd)  { cout << "-1\n"; continue; }
					double yc = d * (P[2].y - P[1].y) / bd + P[1].y;
					double xc = d * (P[2].x - P[1].x) / bd + P[1].x;
					cout << fixed << setprecision(10) << xc << " " << yc << "\n";
					continue;
				}
				double yc = d * (P[2].y - P[3].y) / bd + P[3].y;
				double xc = d * (P[2].x - P[3].x) / bd + P[3].x;
				cout << fixed << setprecision(10) << xc << " " << yc << "\n";
			}
		}
		else cout << "-1\n";
	}
	return 0;
}

```

