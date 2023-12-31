+++
title = '门限签名方案1——SSS(shamir-secret-sharing)  '
date = 2021-12-23 10:28:19  
draft = false
+++

# 门限签名概述

**门限签名是将原本单个的密钥安全地打碎成多个密钥分片，并让每个主体独立使用自己的密钥分片，合作完成所需的密码学协议过程**

# sss（夏米尔密钥共享）介绍

### 1、原理:lagrange polynomial(拉格朗日多项式插值算法)

![](/门限签名方案1——SSS(shamir-secret-sharing)/一次方程.png)

如上图所示：在平面直角坐标系中，有一条直线，且该曲线经过点a(0,4),b(4,0),那么，我们很快能得出该条曲线的唯一表达式为： y = -x + 4，因为我们知道在平面中，两点确定一条直线

<br>
<br>
<br>
<br>


![](/门限签名方案1——SSS(shamir-secret-sharing)/二次方程.png)

再如上图图所示：有一条抛物线，且该抛物线经过点a(0,1),b(2,2),c(3,4),那么，稍微计算一下，

```text
y = ax^2 + bx + c

将3个点的坐标一次带入上述通用方程中
a(0,1) => 1 = a*0^2 + b*0 + 1
b(2,2) => 2 = a*2^2 + b*2 + c
c(3,4) => 4 = a*3^2 + b*3 + c
最终解得该曲线的表达式为：x = (1/2)(x^2 - x + 2)
```

<br>
<br>
<br>
<br>


那么,当曲线是三次曲线呢，甚至更高次幂的曲线，有没有通用的方式，得到其表达式？

答案是有的。

对于上图的抛物线来说，因为其经过了a(0,1),b(2,2),c(3,4)这3个点，我们把这条抛物线称为经过这3点的**二次插值多项式**

这样就引出了插值的数学定义，如下：

【插值的定义】 如果𝑃(𝑥𝑖)=𝑦𝑖(1⩽𝑖⩽𝑛)，那么函数𝑦=𝑃(𝑥)插值了数据点

(𝑥1,𝑦1),⋅⋅⋅,(𝑥𝑛,𝑦𝑛)

简单来讲就是，如果一个函数通过了一组数据点，那么就称这个函数插值了这组数据点。

Lagrange插值公式为：

假设给出𝑛个点(𝑥1,𝑦1),⋅⋅⋅,(𝑥𝑛,𝑦𝑛)，则对于1和n之间的每一个点（x𝑘,yk)可定义

![](/门限签名方案1——SSS(shamir-secret-sharing)/数学定义.png)

𝐿𝑘 的一个有趣性质是：

1. 𝐿𝑘(𝑥𝑘)=1

2. 𝐿𝑘(𝑥𝑗)=0 (𝑗≠𝑘, 点j(𝑥𝑗,𝑦𝑗)为所有给定点中的其他点)

因此，定义𝑛−1次Lagrange多项式为：

![](/门限签名方案1——SSS(shamir-secret-sharing)/多项式方程.png)

验证：求过(𝑥1,𝑦1),(𝑥2,𝑦2),(𝑥3,𝑦3)这3个点的多项式方程：

假设(𝑥1,𝑦1)=a(0,1)，(𝑥2,𝑦2)=b(2,2)，(𝑥3,𝑦3)=c(3,4),则方程k(x)=

![](/门限签名方案1——SSS(shamir-secret-sharing)/验证.png)

将坐标值带入其中：

![](/门限签名方案1——SSS(shamir-secret-sharing)/结果.png)

#### 结论

基于以上的公式，我们得出一个结论：在给点的n个互不相同的点中，必定存在唯一一条次幂小于n的曲线，能够插值这些点。

### 2、应用

![](/门限签名方案1——SSS(shamir-secret-sharing)/公式.png)

如上图所示，这一个多项式方程的通用表达方式，当x的值取0时，即可得到常数值P，sss的原理正是将密钥的值作为P构建一个指定门限值的多项式。

如：想要指定门限值为2-T的密钥共享方案，则应构建一个次幂数为1的多项式：y=ax+P;当执行为3-T的门限时，则构建的多项式为：y=ax^2 + bx +P;

然后，给每个参与方分配一个解(曲线上的点)作为私钥碎片，只有将达到门限数量的解带入到上面的拉格朗日多项式公式中，即可还原多项式，得到私钥。