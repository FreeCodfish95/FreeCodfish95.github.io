---
layout:     post
title:      ati_agrithm
subtitle:   ati
date:       2018-12-10
author:     Codfish95
header-img: img/post-bg-kuaidi.jpg
catalog: true
tags:
    - 算法
    - 姿态解算
---

#组合导航的子系统：姿态解算
##一、捷联惯性导航姿态解算算法分类
* 欧拉角法（三参数法）
* 方向余弦法 （九参数法）
* 四元数法（四参数法）
* 等效旋转矢量法
    1. 三子样算法
    2. 四子样算法
* 改进四元数法
    1. 四阶龙格库塔法求解*四元数微分方程*
    2. 四阶毕卡逼近法求解*四元数微分方程*
    3. 罗德里格参数法
    4. 三阶泰勒展开递推算法

##二、展示算法
1. 四元数算法（可以看作单子样的等效旋转矢量法）
2. 等效旋转矢量法（三子样，四子样）
3. 改进四元数算法（龙格库塔法)
3. 改进四元数算法（毕卡逼近法）

##三、先前知识 
1. 描述姿态运动的微分方程是非线性的，其离散化的求解会引起转动不可交换误差。
2. 传感器采用角增量信号输出，利用角增量构造等效旋转矢量以补偿和降低不可交换误差。
3. 低成本航姿参考系统（AHRS）由于其采用的MEMS传感器精度低，其数学模型可以简化。
##四、数学知识
1. $(V\times)$表示反对称矩阵，反对称矩阵与向量相乘等于向量自身的外积或者说是叉乘。
    $$(\vec{V}\times)\vec{V}=\vec{V}\times \vec{V}$$

2. $C_b^i$为变换矩阵，令 $\phi=\left|\vec{\phi}\right|$
$$ C_b^i=I+\frac{\sin\phi}{\phi}(\vec{\phi}\times)+\frac{1-\cos \phi}{\phi^2}(\vec{\phi}\times)^2$$ 

把$C_i^b$看作是等效旋转矢量$\vec{\phi}$的函数$C_b^i=M_{RV}(\vec{\phi})$
##五、姿态更新算法

选择“东北天”地理坐标系作为捷联惯导系统导坐标系，计为$n$系，机体坐标系计为$b$系,惯性坐标系计为$i$系，惯性坐标系为绝对坐标系，不随时间变化。以$n$系作为参考系的姿态微分方程为
    
$$\dot{C}_b^n=C_b^n(\omega_{nb}^b\times)\tag{1.1}$$

式子（1.1）递推最终可得到

$$C_{b(m)}^{n(m)}=C_{n(m-1)}^{n(m)} C_{b(m-1)}^{n(m-1)} C_{b(m)}^{b(m-1)} \tag{1.2}$$
* $C_{b(m)}^{n(m)}$ 表示**m时刻**的<font color="red">**机体坐标系**</font>到<font color="green">**导航坐标系**</font>的转换矩阵
<br/>
*  $C_{b(m-1)}^{n(m-1)}$ 表示**m-1时刻**的<font color="red">**机体坐标系**</font>到<font color="green">**导航坐标系**</font>的转换矩阵
<br/>
* $C_{n(m-1)}^{n(m)}$ 表示以$i$系作为参考基准，$n$系从的<font color="red">**m时刻**</font>到<font color="green">**m-1时刻**</font>的旋转变换矩阵
<br/>
* $C_{b(m-1)}^{b(m-1)}$ 表示以$i$系作为参考基准，$b$系从的<font color="red">**m-1时刻**</font>到<font color="green">**m时刻**</font>的旋转变换矩阵
根据等效旋转矢量法可得
$$C_{n(m-1)}^{n(m)}=M_{RV}^T(\vec{\phi}_{in(m)}^n)\approx M_{RV}^T(T\omega_{in(m)}^{n}) \tag{1.3}$$
$$C_{b(m)}^{b(m-1)}=M_{RV}(\vec{\phi}_{ib(m)}^b) \tag{1.4}$$
最终公式可可以化简为

$$C_{b(m)}^{n(m)}= M_{RV}^T(T\omega_{in(m)}^{n}) C_{b(m-1)}^{n(m-1)} M_{RV}(\vec{\phi}_{ib(m)}^b) \tag{1.5}$$

对于低成本的AHRS，一般用在低速飞行器上所以导航坐标系相对惯性坐标系的变化量很小，而且由于采用精度较低的MEMS传感器，导致其误差较大，掩盖了地球自转所造成的误差。所以令$\omega_{in(m)}^n$为0，式子（1.5）可以化简为

$$C_{b(m)}^{n(m)}= C_{b(m-1)}^{n(m-1)} M_{RV}(\vec{\phi}_{ib(m)}^b) \tag{1.6}$$




 

