---
author: "cyx20080216"
title: "题解 P2013 无线电测向"
date: 2021-08-08T21:06:20+08:00
lastmod: 2021-08-08T21:06:20+08:00
tags: ["题解"]
categories: ["题解"]
---
## 题目分析
读完题后，很容易就能看出来，这道题的核心是**求两直线交点**

在这之前，我们要先算出这两条直线

显然，这两条直线是给定的两座灯塔与船的连线（~~废话~~），可惜我们不知道船的位置

我们虽然不知道船的具体位置，但是题目给了我们足以算出船相对与灯塔的方向的信息。所以，我们可以在这个方向或是反方向上取任意一点，因为**同一直线上取任意两点计算得到的直线都是原直线**
## 具体步骤如下
这里以灯塔1为例

我们用斜截式 $y=ax+b$ 表示该直线

用 $x_{Light}$ 和 $y_{Light}$ 表示灯塔1的坐标

用 $\theta_{Boat}$ 表示船的航向

用 $\theta_{BoatToLight}$ 分别表示以船的航向为 $0^{\circ}$ 下的灯塔相对于船的方向

用 $\theta_{LightToBoat}$ 分别表示的船相对于灯塔的方向

用 $x_{FrontOfLight}$ 和 $y_{FrontOfLight}$ 表示位于灯塔1 $\theta_{LightToBoat}$ 方向1单位长度的点的坐标

画画图就可以轻易推出 $\theta_{LightToBoat}=\theta_{Boat}+\theta_{BoatToLight}+180^{\circ}$

利用 $\theta_{LightToBoat}$ 我们又可以求出 $x_{FrontOfLight}=x_{Light}+sin\theta_{LightToBoat}$ 和 $y_{FrontOfLight}=y_{Light}+cos\theta_{LightToBoat}$

注：这里其实跟极坐标系差不多，只不过**方向不一样**

所以， $a=\frac{y_{Light}-y_{FrontOfLight}}{x_{Light}-x_{FrontOfLight}}$ $b=y_{Light}-ax_{Light}$

## Code
```cpp
#include<bits/stdc++.h>
#define Pi 3.1415926535897932384626
using namespace std;
struct Point
{
	double x,y;
	Point(const double &x=0.0,const double &y=0.0):
		x(x),
		y(y)
	{
	}
};
struct Line
{
	double a,b;
	Line(const double &a=0.0,const double &b=0.0):
		a(a),
		b(b)
	{
	}
};
inline Line twoPointToLine(const Point &a,const Point &b)
{
	return Line((a.y-b.y)/(a.x-b.x),a.y-(a.y-b.y)/(a.x-b.x)*a.x);
}
inline Point twoLineToPoint(const Line &a,const Line &b)
{
	return Point(-(a.b-b.b)/(a.a-b.a),-a.a*(a.b-b.b)/(a.a-b.a)+a.b);
}
Point light1,light2;
double boatFOV;
double light1FOV,light2FOV;
inline void init()
{
	int n;
	map<string,Point> light;
	scanf("%d",&n);
	for(int i=1;i<=n;i++)
	{
		string lightName;
		Point point;
		cin>>lightName;
		scanf("%lf %lf",&point.x,&point.y);
		light.insert(make_pair(lightName,point));
	}
	scanf("%lf",&boatFOV);
	string lightName;
	cin>>lightName;
	light1=light.at(lightName);
	scanf("%lf",&light1FOV);
	cin>>lightName;
	light2=light.at(lightName);
	scanf("%lf",&light2FOV);
}
inline void solve()
{
	Point frontOfLight1(light1.x+sin((boatFOV+light1FOV+180)*Pi/180.0),light1.y+cos((boatFOV+light1FOV+180)*Pi/180.0));
	Point frontOfLight2(light2.x+sin((boatFOV+light2FOV+180)*Pi/180.0),light2.y+cos((boatFOV+light2FOV+180)*Pi/180.0));
	Line light1ToBoat(twoPointToLine(light1,frontOfLight1));
	Line light2ToBoat(twoPointToLine(light2,frontOfLight2));
	if(light1ToBoat.a!=light2ToBoat.a)
	{
		Point boat(twoLineToPoint(light1ToBoat,light2ToBoat));
		printf("%.2lf %.2lf\n",boat.x,boat.y);
	}
	else
		printf("NO ANSWER\n");
}
int main()
{
	init();
	solve();
	return 0;
}

```
