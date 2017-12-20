---
layout:     post
title:      "C++ 模板特化"
subtitle:   "全特化与偏特化"
date:       2017-12-11 22:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---

# 简介
模板主要有函数模板和类模板，特化指给初等模板定义一些特例。若完全指定类型，为全特化，否则为偏特化。唯一需要注意的是函数模板无法进行偏特化，但可以通过函数重载获得类似的功能。<br>
全特化语法:
> template <> declaration

偏特化语法:
> template < parameter-list > class-key class-head-name < argument-list > declaration

<b>特化使我们可以使用C++黑魔法 --- 模板元编程</b>

# 示例
{% highlight cpp %}
// 1. 类的偏特化

template<class T1, class T2, int I>
class A {};                // 初等模板

 
template<class T, int I>
class A<T, T*, I> {};      // #1 ：偏特化，其中 T2 是指向 T1 的指针


template<class T, class T2, int I>
class A<T*, T2, I> {};     // #2 ：偏特化，其中 T1 是指针


template<class T>
class A<int, T*, 5> {};    // #3 ：偏特化，其中 T1 是 int ， I 是 5 ，而 T2 是指针


template<class X, class T, int I>
class A<X, T*, I> {};      // #4 ：偏特化，其中 T2 是指针



// 2. 类的全特化

template<>
class A<int, char, 5> {};  // #3 ：偏特化，其中 T1 是 int ， T2 是 char ，I 是 5



// 3. 函数的全特化

template<typename T1, typename T2>
void fun(T1, T2) {}

template<>
void fun<int,char>(int, char) {}  // 全特化


template<typename T>
void fun<int,T>(int, T) {}        // 错误，函数模板不能偏特化


template<typename T>
void fun(int, T) {}               // 注意，这里不是特化，是函数重载！！


// （kramdown简直智障，注释后面还要加回车，最后一行也有毛病）

// i have to add this line for an unknown bug
{% endhighlight %}
