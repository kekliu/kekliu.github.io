---
layout:     post
title:      "C++之何时调用构造函数、析构函数、拷贝构造函数、移动构造函数"
subtitle:   ""
date:       2017-12-21 22:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


### 1. 简介
本示例演示了调用构造函数、析构函数、拷贝构造函数、赋值函数的时机，这对于我们对内存的精确控制非常重要。<br>
测试环境说明：
> OS: Linux<br>
> Compiler: clang 3.8.0（<b>请加上`-std=c++11`开启对移动语义的支持和`-fno-elide-constructors`防止下面的移动构造函数被编译器NRVO优化掉</b>）<br>
> Build Type: Release

### 2. 示例
{% highlight cpp %}
#include <iostream>

using std::cout;
using std::endl;

class C {
public:
  C() { cout << "constructor       " << this << endl; }

  C(const C &p) {
    cout << "copy constructor  " << &p << " -> " << this << endl;
  }

  C(const C &&p) {
    cout << "move constructor  " << &p << " -> " << this << endl;
  }

  ~C() { cout << "deconstructor     " << this << endl; }

  C &operator=(const C &p) {
    cout << "operator =        " << &p << " -> " << this << endl;
    return *this;
  }

  C &operator=(const C &&p) {
    cout << "move operator =   " << &p << " -> " << this << endl;
    return *this;
  }

  C return_local_obj() {
    C c;
    return c;
  }

  C &return_local_obj_ref() {
    C c;
    return c; // don't return a local variable reference !!
  }

  C &return_self_obj() { return *this; }

  void accept_obj_as_param(const C c) { this->value = c.value; }

  void accept_obj_ref_as_param(const C &c) { this->value = c.value; }

private:
  int value;
};

void class_internal_funcs_test() {
  cout << "1. constructor only" << endl;
  C a;

  cout << "2. copy constructor" << endl;
  C b = a;

  {
    cout << "3. move constructor" << endl;
    C c = std::move(a);
  }

  {
    cout << "4. assignment operator" << endl;
    b = a;
  }

  cout << "5. function test" << endl;

  {
    cout << "::accept_obj_as_param(C)" << endl;
    a.accept_obj_as_param(b);
  }

  {
    cout << "::accept_obj_ref_as_param(C&)" << endl;
    a.accept_obj_ref_as_param(b);
  }

  {
    cout << "::return_local_obj()" << endl;
    C c = a.return_local_obj();
    cout << "::return_local_obj() v2" << endl;
    c = a.return_local_obj();
  }

  {
    cout << "::return_local_obj_ref()" << endl;
    C c = a.return_local_obj_ref();
    cout << "::return_local_obj_ref() v2" << endl;
    c = a.return_local_obj_ref();
  }

  {
    cout << "::return_self_obj()" << endl;
    C c = a.return_self_obj();
    cout << "::return_self_obj() v2" << endl;
    c = a.return_self_obj();
  }

  cout << "end of program" << endl;
}
{% endhighlight %}
程序运行结果:
``` plaintext
1. constructor only
constructor       0x7ffddaedad48
2. copy constructor
copy constructor  0x7ffddaedad48 -> 0x7ffddaedad40
3. move constructor
move constructor  0x7ffddaedad48 -> 0x7ffddaedad38
deconstructor     0x7ffddaedad38
4. assignment operator
operator =        0x7ffddaedad48 -> 0x7ffddaedad40
5. function test
::accept_obj_as_param(C)
copy constructor  0x7ffddaedad40 -> 0x7ffddaedad30
deconstructor     0x7ffddaedad30
::accept_obj_ref_as_param(C&)
::return_local_obj()
constructor       0x7ffddaedad50
move constructor  0x7ffddaedad50 -> 0x7ffddaedad20
deconstructor     0x7ffddaedad50
move constructor  0x7ffddaedad20 -> 0x7ffddaedad28
deconstructor     0x7ffddaedad20
::return_local_obj() v2
constructor       0x7ffddaedad58
move constructor  0x7ffddaedad58 -> 0x7ffddaedad18
deconstructor     0x7ffddaedad58
move operator =    0x7ffddaedad18 -> 0x7ffddaedad28
deconstructor     0x7ffddaedad18
deconstructor     0x7ffddaedad28
::return_local_obj_ref()
constructor       0x7ffddaedad60
deconstructor     0x7ffddaedad60
copy constructor  0x7ffddaedad60 -> 0x7ffddaedad10
::return_local_obj_ref() v2
constructor       0x7ffddaedad68
deconstructor     0x7ffddaedad68
operator =        0x7ffddaedad68 -> 0x7ffddaedad10
deconstructor     0x7ffddaedad10
::return_self_obj()
copy constructor  0x7ffddaedad48 -> 0x7ffddaedad08
::return_self_obj() v2
operator =        0x7ffddaedad48 -> 0x7ffddaedad08
deconstructor     0x7ffddaedad08
end of program
deconstructor     0x7ffddaedad40
deconstructor     0x7ffddaedad48
```
建议写法:
* 函数形参尽量用引用，例如上面的`void C::accept_obj_ref_as_param(const C &c)`；
* <b>函数绝对不能返回局部对象的引用</b>，例如上面的`C &return_local_obj_ref()`，这样写是非常危险的，以`C c = a.return_local_obj_ref()`生成的汇编代码为例，程序先通过构造函数构造了一个临时对象`tmp_obj`，然后还没有将临时对象`tmp_obj`的值传给`c`就直接调用析构函数删除了临时对象，这可能会导致不可预测的结果；
* 返回临时对象的最好的方式是返回对象而非引用，使用普通变量或右值引用变量获取返回值，例如上面的`C &return_local_obj()`和`C c = a.return_local_obj()。</b>
* C++11的右值引用的特性试图实现移动语义和完美转发的功能，其中完美转发功能是由右值引用和引用折叠一起实现的。
