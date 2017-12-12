---
layout:     post
title:      "C++ 模板参数包"
subtitle:   ""
date:       2017-12-12 13:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


### 1. 语法
#### 1.1 模板参数包

|------------------------------------------------------------------------+---------------------|
| Declaration Format                                                     |                     |
|:-----------------------------------------------------------------------|:--------------------|
| type ... Args(optional)                                                | (1) (since C++11)   |
| typename\|class ... Args(optional)                                     | (2) (since C++11)   |
| template < parameter-list > typename(C++17)\|class ... Args(optional)  | (3) (since C++11)   |
|-----------------------------------------------------------------------+---------------------|

#### 1.2 函数参数包
> Args ... args(optional)

#### 1.3 模板参数展开
> pattern ...

#### 1.4 例子
{% highlight cpp %}
// 1. 模板参数包

template<class ... Types> struct Tuple {};
Tuple<> t0;           // Types 不包含实参

Tuple<int> t1;        // Types 包含一个实参： int

Tuple<int, float> t2; // Types 包含二个实参： int 与 float

Tuple<0> error;       // 错误： 0 不是类型

// 2. 函数参数包### 1. 语法
#### 1.1 模板参数包

template<class ... Types> void f(Types ... args);
f();       // OK ： args 不包含实参

f(1);      // OK ： args 包含一个实参： int

f(2, 1.0); // OK ： args 包含二个实参： int 与 double

// 3. 在初等类模板中，模板参数包必须是模板形参列表的最后一个形参。在函数模板中，模板参数包可以在列表中早于所有能从函数实参推导的参数出现，或拥有默认参数。

template<typename... Ts, typename U> struct Invalid; // 错误： Ts.. 不在结尾


template<typename ...Ts, typename U, typename=void>
void valid(U, Ts...);     // OK ：能推导 U

// void valid(Ts..., U);  // 不能使用： Ts... 在此位置是非推导语境


valid(1.0, 1, 2, 3);      // OK ：推导 U 为 double ， Ts 为 {int,int,int}

//
{% endhighlight %}

### 2. 包展开
<b>后随省略号的模式，其中至少一个参数包名出现至少一次者，被展开成零或更多个逗号分隔的模式实例，其中参数包被按顺序替换成每个来自包的元素。</b><br>
<b>e.g. pattern ... 可以解包为 pattern1, pattern2, ... , patternN</b>
{% highlight cpp %}
template<class ...Us> void f(Us... pargs) {}
template<class ...Ts> void g(Ts... args) {
    f(&args...); // “&args...” 是包展开

                 // “&args” 是其模式

}
g(1, 0.2, "a"); // Ts... args 展开成 int E1, double E2, const char* E3

                // &args... 展开成 &E1, &E2, &E3

                // Us... 展开成 int* E1, double* E2, const char** E3

//
{% endhighlight %}
{% highlight cpp %}
f(&args...); // 展开成 f(&E1, &E2, &E3)

f(n, ++args...); // 展开成 f(n, ++E1, ++E2, ++E3);

f(++args..., n); // 展开成 f(++E1, ++E2, ++E3, n);

f(const_cast<const Args*>(&args)...);
// f(const_cast<const E1*>(&X1), const_cast<const E2*>(&X2), const_cast<const E3*>(&X3))

f(h(args...) + args...);
// 展开成 f(h(E1,E2,E3) + E1, h(E1,E2,E3) + E2, h(E1,E2,E3) + E3)

//
{% endhighlight %}
