---
layout:     post
title:      "C++ 模板特化例子之 type_descriptor"
subtitle:   ""
date:       2017-12-20 21:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


### 1. 原理
本示例原理位于之前的“C++模板特化”一节，主要演示了函数模板的全特化和偏特化，请注意，这里没有用到重载。

### 2. 示例
{% highlight cpp %}
template <class T> struct type_descriptor {};

// 基础模板

template <class T>
std::ostream &operator<<(std::ostream &out, type_descriptor<T>) {
  return out << "UNKNOWN";
}

// 偏特化，实现对指针、引用、函数和数组的描述

template <class T>
std::ostream &operator<<(std::ostream &out, type_descriptor<T *>) {
  return out << "* -> " << type_descriptor<T>();
}

template <class T>
std::ostream &operator<<(std::ostream &out, type_descriptor<T &>) {
  return out << "& -> " << type_descriptor<T>();
}

template <class T>
std::ostream &operator<<(std::ostream &out, type_descriptor<T()>) {
  return out << "function() -> " << type_descriptor<T>();
}

template <class Tin, class Tout>
std::ostream &operator<<(std::ostream &out, type_descriptor<Tout(Tin)>) {
  return out << "function(" << type_descriptor<Tin>() << ") -> "
             << type_descriptor<Tout>();
}

template <class T>
std::ostream &operator<<(std::ostream &out, type_descriptor<T[]>) {
  return out << "[] -> " << type_descriptor<T>();
}

// 全特化，实现对基本类型的描述

template <> std::ostream &operator<<(std::ostream &out, type_descriptor<int>) {
  return out << "int";
}

template <> std::ostream &operator<<(std::ostream &out, type_descriptor<char>) {
  return out << "char";
}

template <>
std::ostream &operator<<(std::ostream &out, type_descriptor<short>) {
  return out << "short";
}

template <> std::ostream &operator<<(std::ostream &out, type_descriptor<long>) {
  return out << "long";
}

template <> std::ostream &operator<<(std::ostream &out, type_descriptor<void>) {
  return out << "void";
}

template <>
std::ostream &operator<<(std::ostream &out, type_descriptor<float>) {
  return out << "float";
}

template <>
std::ostream &operator<<(std::ostream &out, type_descriptor<double>) {
  return out << "double";
}

// 测试函数

void type_descriptor_test() {
  cout << type_descriptor<int (*(*)[])(float)>() << endl;
}
{% endhighlight %}
运行`type_descriptor_test()`，结果为`* -> [] -> * -> function(float) -> int`，表示该类型是一个指针，指向数组，数组元素为指针，指向f(float)类型的函数，函数返回int
