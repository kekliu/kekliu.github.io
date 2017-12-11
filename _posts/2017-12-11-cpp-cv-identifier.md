---
layout:     post
title:      "C++中的cv类型限定符"
subtitle:   "c = const  v = volatile"
date:       2017-12-11 19:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---

# 简介
> const ——定义类型为常量。<br>
> volatile ——定义类型为易变。volatile对应的是special memory，告诉编译器不要把volatile修饰的数据优化到寄存器里，每次读取和写入都操作原始的数据地址。<b>不要将volatile用于多线程的场合！！</b><br>
> mutable ——应用于非引用非 const 类型的非静态类成员，并指定该成员不影响类的外部可见状态（常用于互斥、内存缓存、惰性求值、及访问设备）。 const 类实例的 mutable 成员是可修改的。（注意： C++ 语言文法将 mutable 当做 storage-class-specifier （存储类指定符），但它不影响存储类。）<br>

# cv 对象
const 对象——类型为 const-qualified 的对象，或 const 对象的非 mutable 子对象。这种对象不能修改：尝试直接这么做是编译时错误，且尝试间接这么做（例如通过到非 const 类型的引用或指针修改 const 对象）导致未定义行为。<br>
volatile 对象——类型为 volatile-qualified 的对象，或 volatile 对象的子对象，或 const volatile 对象的 mutable 子对象。通过 volatile 限定类型泛左值表达式的每次访问（读或写操作、成员函数调用等）都被当作对于优化目的的可见副效应（即在执行的单一线程内， volatile 访问不能被优化掉或者与另一先序或后序于该 volatile 访问的可见副效应重排序。这使得 volatile 对象适用于与信号处理函数的交流，但不适于与另一执行线程交流，参阅 std::memory_order ）。尝试通过非 volatile 泛左值引用 volatile 对象（例如，通过到非 volatile 类型的引用或指针）导致未定义行为。<br>
const volatile 对象——类型为 const-volatile-qualified 的对象， const volatile 对象的非 mutable 子对象， volatile 对象的 const 子对象，或非 mutable 对象的 const 子对象。表现同 const 对象与 volatile 对象两者。

# const 关键字的其它用法
{% highlight cpp %}
// 1. 修饰变量

int i;
const int * p1 = &i;        // *p1 不可变, 必须初始化

int * const p2 = &i;        // p2 不可变

const int * const p2 = &i;  // p3 和 *p3 都不可变


// 2. 修饰参数和返回值，表示参数或返回值不可变


// 3. 类

// a. 成员变量

// 必须自行初始化或在构造函数中初始化

// b. 成员函数

// const成员函数可以访问const或non-const对象的所有数据成员

// non-const成员函数只能访问非const对象的所有数据成员

// const修饰的可以作为无const修饰函数的重载，const函数由const对象调用，非const由非const对象调用

// e.g. 若func()有const和non-const两个重载，则第一个调用non-const，第二个调用const

C c1;
c1.func();
const C c2;
c2.func();
// i have to add this line for an unknown bug
{% endhighlight %}
