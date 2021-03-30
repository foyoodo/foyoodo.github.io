---
title: "单例模式"
date: 2020-05-21T10:38:54+08:00
lastmod: 2020-05-21T15:19:54+08:00
draft: false
tags: ["设计模式"]
---

单例模式，也叫单子模式。实现思路是：一个类能返回对象一个引用（永远是同一个）和一个获得该实例的方法（必须是**静态方法**，通常使用 `getInstance` 这个名称）。

<!--more-->

单例模式在**多线程**的应用场合下必须小心使用。如果当唯一实例尚未创建时，有两个线程同时调用创建方法，那么它们同时没有检测到唯一实例的存在，从而同时各自创建了一个实例，这样就有两个实例被构造出来，从而违反了单例模式中实例唯一的原则。 解决这个问题的办法是为指示类是否已经实例化的变量提供一个**互斥锁**（虽然这样会降低效率）。

## C++

懒汉方式：

```cpp
class singleton {
public:
  static singleton* getInstance(int num, char ch) {
    if (!_instance)
      _instance = new singleton(num, ch);
    return _instance;
  }
private:
  int _num;
  char _ch;
  static singleton *_instance;
  singleton(int num, char ch) : _num(num), _ch(ch) {}
}
```
