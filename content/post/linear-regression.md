---
author: "cyx20080216"
title: "线性回归"
date: 2022-04-14T10:49:00+08:00
lastmod: 2022-04-14T22:54:00+08:00
tags: ["算法", "机器学习"]
categories: ["机器学习"]
---
# 问题
有 $n$ 个点，坐标分别为 $(x_1,y_1)$ , $(x_2,y_2)$ ... $(x_n,y_n)$

有一个函数，格式为 $f(x)=kx$

有一个损失值，计算方式为 $l=\sum_{i=1}^n(f(x_i)-y_i)^2$

如何找到一个使 $l$ 最小的 $k$ ？
# 梯度下降
一个简单的方法是选择一个随机的 $k$ ，然后不断改变 $k$ 使得 $l$ 降低

为了加快这一过程，可以利用梯度（也就是微分）来计算应该改变多少

我们设定一个**超参数**：**学习率** $\eta$

当学习率较大时，收敛速度会快，但是不稳定

当学习率较小时，收敛速度会慢，但是稳定

通常取 $\eta=0.01$

每次让 $k$ 移动 $-\dfrac{dl}{dk}\eta$

最后就可以得到较优的 $k$
# 计算梯度
这一步只需要简单的微分知识，可以得到 $\dfrac{dl}{dk}=\sum_{i=1}^n2x_i(f(x_i)-y_i)$
# 进阶
如果 $f(x)=kx+b$ ，就有了两个参数，但做法依然类似，不过是多处理一个参数

每次让 $k$ 移动 $-\dfrac{dl}{dk}\eta$

每次让 $b$ 移动 $-\dfrac{dl}{db}\eta$

$\dfrac{dl}{dk}=\sum_{i=1}^n2x_i(f(x_i)-y_i)$

$\dfrac{dl}{db}=\sum_{i=1}^n2(f(x_i)-y_i)$

这样便可以得到较优的 $k$ 和 $b$

# 代码
```go
package main

import "fmt"

type Point struct {
	x, y float64
}

const learningRate = 0.01

func solve(point []Point) (float64, float64) {
	a := 0.0
	b := 0.0
	for i := 0; i < 10000; i++ {
		aGrad := 0.0
		bGrad := 0.0
		for _, each := range point {
			aGrad += 2 * each.x * (a*each.x + b - each.y)
			bGrad += 2 * (a*each.x + b - each.y)
		}
		a = a - aGrad * learningRate
		b = b - bGrad * learningRate
	}
	return a, b
}

func main() {
	var n int
	var point []Point
	fmt.Scanf("%d\n", &n)
	point = make([]Point, n)
	for i := 0; i < n; i++ {
		fmt.Scanf("%f %f\n", &point[i].x, &point[i].y)
	}
	a, b := solve(point)
	fmt.Printf("%f %f\n", a, b)
}
```
