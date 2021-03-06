---
title: "抽象工厂模式 vs. 观察者模式"
date: 2020-05-25T16:01:23+08:00
lastmod: 2020-06-08T19:14:23+08:00
draft: false
tags: ["设计模式"]
---

抽象工厂模式与观察者模式的相同点和不同点。

<!--more-->

## 相同点

1. 都是经典的设计模式，对应常见的应用场景；
2. 基于 UML 完成描述，至少说明类视图的两类实体、六大关系是通用的；
3. 贯彻了分而治之；
4. 局部内强耦合，局部之间弱耦合；
5. 局部之间的链接发生在顶层接口（抽象类）之间。

## 不同点

1. 解决不同场景；
2. 局部之间的弱耦合关系不尽相同：usage dependency vs. association；

## 知识点

- 分而治之，对应不同的项目、文件，是实施分工的依据；
- 抽象类、多态在两个模式的实现中都扮演了“关键先生”；
- 设计模式所指导的“面向接口的编程”（interface-oriented programming）是工程中喜闻乐见的，是“对付”改变的基石。
