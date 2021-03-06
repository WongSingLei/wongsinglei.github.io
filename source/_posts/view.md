---
title: 数据库——视图的作用
comment: true
date: 2020-04-08 15:47:40
updated: 2020-04-08 15:47:40
categories: 数据库
tags:
---

### 视图的定义

视图（view）：从一个或几个基本表或者视图中根据用户需求而导出的虚拟的表，其内容由查询决定。视图具有普通表的结构，但是不实现数据存储。

对视图的修改：单表视图一般用于查询和修改，会改变基本表的数据。多表视图一般用于查询，不会改变基本表的数据。

### 作用

1、可以定制用户数据，聚焦特定数据。

2、简化数据操作，把经常使用的数据定义为视图。

3、安全性，用户只能查询和修改能看到的数据。

4、逻辑数据独立性，屏蔽了真实表结构变化带来的影响。

> 视图可以使应用程序和数据库表在一定程度上相互独立。如果没有视图，应用是建立在表上的。而有了视图之后，程序可以建立在视图上，从而程序与数据库表被视图分割开来。

5、合并分离的数据，创建分区视图。

### 缺点

1、性能差

数据库必须把视图查询转化成对基本表的查询，如果这个视图是由一个复杂的多表查询所定义，那么，即使是视图的一个简单查询，数据库也要把它变成一个复杂的结合体，需要花费一定的时间。

2、修改限制

当用户试图修改视图的某些信息时，数据库必须把它转化为对基本表的某些信息的修改，对于简单的视图来说，这是很方便的，但是，对于比较复杂的试图，可能是不可修改的。