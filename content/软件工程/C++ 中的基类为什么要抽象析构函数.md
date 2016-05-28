---
title: C++ 中的基类为什么要抽象析构函数
layout: page
date: 2015-03-12 11:40
---

[TOC]

在 Google 编程规范 中提到过: 用来做基类的类, 其析构函数应该为虚函数.

那么为什么呢? 看下面这个例子:

```cpp
#include "stdafx.h"
#include <iostream>
using namespace std;

class ClxBase {
public:
    ClxBase() {};
    virtual ~ClxBase() {};

    virtual void DoSomething() {
        cout "Do something in class ClxBase!" << endl;
    };
};

class ClxDerived
    : public ClxBase {
public:
    ClxDerived() {};
    ~ClxDerived() {
        cout << "Output from the destructor of class ClxDerived!" << endl;
    };

    void DoSomething() {
        cout << "Do something in class ClxDerived!" << endl;
    };
};

int _tmain(int argc, _TCHAR* argv[]) {
    ClxBase *pTest = new ClxDerived;
    pTest->DoSomething();
    delete pTest;

    system("PAUSE");
    return 0;
}
```
<br>
输出结果是:

```
Do something in class ClxDerived!

Output from the destructor of class ClxDerived!
```
<br>
这个很简单, 非常好理解.

**但是,** 如果把类 ClxBase 析构函数前的 virtual 去掉, 那输出结果就是下面的样子了:

```
Do something in class ClxDerived!
```
<br>
也就是说类 ClxDerived 的析构函数根本没有被调用.

为什么没有被调用, 这也很好理解, 因为 pTest 是 ClxBase 类型, 它的析构不是虚函数的话, 也就不能实现多态.

一般情况下类的析构函数里面都是释放内存资源, 而析构函数不被调用的话就会造成内存泄漏.

至于子类没有额外数据成员, 甚至父类也没有任何数据成员的特殊情况下, 析构函数的调用是否必要是语义争论, 从编程设计规范的角度看, 在含有虚函数的父类中, 定义虚析构函数绝对必要.

当然, 并不是要把所有类的析构函数都写成虚函数. 因为当类里面有虚函数的时候, 编译器会给类添加一个虚函数表, 里面来存放虚函数指针, 这样就会增加类的存储空间.
所以, 只有当一个类被用来作为基类的时候, 才把析构函数写成虚函数.
