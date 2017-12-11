---
layout:     post
title:      "C++ 模板形参和模板实参"
subtitle:   ""
date:       2017-12-11 16:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


# 模板形参和模板实参

声明语法：
template < parameter-list > declaration

## 模板形参
### 非类型模板形参

|---------------------------------+----------------------|
| Declaration Format              |                      |
|:--------------------------------|:---------------------|
| type name(optional)             | (1)                  |
| type name(optional) = default   | (2)                  |
| type ... name(optional)         | (3) (since C++11)    |
| auto name                       | (4) (since C++17)    |
|---------------------------------+----------------------|

type is one of the following types:
> std::nullptr_t (since C++11);  
> integral type;  
> lvalue reference type (to object or to function);  
> pointer type (to object or to function);  
> pointer to member type (to member object or to member function);  
> enumeration type.

### 类型模板形参

|-----------------------------------------------------------------|
| Declaration Format                       |                      |
|------------------------------------------|----------------------|
| typename name(optional)                  | (1)                  |
| class name(optional)                     | (2)                  |
| typename\|class name(optional) = default | (3)                  |
| typename\|class ... name(optional)       | (4) (since C++11)    |
|-----------------------------------------------------------------|

### 模板模板形参

|---------------------------------------------------------------------------------------------------|
| Declaration Format                                                          |                     |
|-----------------------------------------------------------------------------|---------------------|
| template < parameter-list > typename(C++17)\|class name(optional)           | (1)                 |
| template < parameter-list > typename(C++17)\|class name(optional) = default | (2)                 |
| template < parameter-list > typename(C++17)\|class ... name(optional)       | (3) (since C++11)   |
|---------------------------------------------------------------------------------------------------|

### 约束模板形参


## 模板实参
### 模板非类型实参
### 模板类型实参
### 模板模板实参
