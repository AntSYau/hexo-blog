---
title: Spatial Analysis Using GIS 复习提纲
date: 2019-08-07 13:23:24
mathjax: true
categories:
- ffuck
tags: 
- GIS
- VSP
---

课程：Spatial Analysis Using GIS
课程编号：SCARP VSP Term 2
讲师：June Skeeter
时间：16/vii ~ 6/viii, '19

# Lecture 1 Course Intro

## What is GIS?

> A geographic information system (GIS) is a **framework** for gathering, managing, and analyzing data. Rooted in the **science of geography**, GIS integrates many types of data. It analyzes spatial location and organizes layers of information into **visualizations** using maps and 3D scenes. With this unique capability, GIS reveals deeper insights into data, such as patterns, relationships, and situations — helping users **make smarter decisions**.

# Lecture 2 Fundamentals of GIS

## Raster vs. Vector

栅格模型（Raster Model）用于描述连续现象（Continuous Phenomena），向量模型（Vector Model）用于描述离散物体（Discrete Objects）。

栅格模型由不同的图层（bands）构成，每个图层内的每个像素（cells）只能代表单一属性的单一值；向量模型间可堆叠、可形成封闭区域，每个物体可以有多个属性（对建筑来说，名字、地址、所有人、高度等）。

## GIS Data Types

### Vectors: Point Data

点数据是零维的（zero-dimensional），没有长宽高（但是现实物体可能有这些属性）。

点拥有$(x,y)$坐标对（coordinate pair）描述它们的位置。

### Vectors: Line Data

线数据是一维的（one-d…），只有长度。

线数据包含<u>至少</u>两点（起点和终点）和路径（direction）。

线数据可能有一系列经过点，称为节点（vertices）。

线可能是直的或者弯♂的。

直线在GIS中按照一系列点的坐标形成的有序坐标对的形式存储。

### Vectors: Polygon Data

多边形数据是二维的（two-d…），有长宽。

多边形至少包含三个点，起点和终点必须相同（形状封闭）。

多边形包含多个线数据，称为边（edges）。

多边形在GIS中按照一系列点的坐标形成的有序坐标对的形式存储，但是第一个点和最后一个点坐标相同。

### Circles & Ellipses

圆和椭圆在GIS中存储为$(\text{圆心}(x,y),\text{半长轴}r_M,\text{半短轴}r_m)$。

## Tobler's Law

>*Everything is related to everything else, but near things are more related than distant things.*
>— Waldo Tobler

地理学第一定律：地物之间的相关性与距离有关，一般来说，距离越近，地物间相关性越大；距离越远，地物间相异性越大。

# Lecture 3 Uncertainty in GI

## Map Projections

