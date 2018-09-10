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
本示例演示了C++类中的构造函数、析构函数、拷贝构造函数、移动构造函数、赋值函数、移动赋值函数的调用时机。<br>
测试环境说明：
> OS: Linux<br>
> Compiler: clang 3.8.0<br>
> Options: <b>加上`-std=c++11`开启对移动语义的支持，不要使用`-fno-elide-constructors`以确保编译器开启NRVO优化</b>）<br>
> Build Type: Release

### 2. 示例
```
#include <iostream>

using std::cout;
using std::endl;

class C {
 public:
  C() { cout << "constructor         " << this << endl; }

  C(const C &p) {
    cout << "copy constructor    " << &p << " -> " << this << endl;
  }

  C(const C &&p) {
    cout << "move constructor    " << &p << " -> " << this << endl;
  }

  ~C() { cout << "deconstructor       " << this << endl; }

  C &operator=(const C &p) {
    cout << "operator =          " << &p << " -> " << this << endl;
    return *this;
  }

  C &operator=(const C &&p) {
    cout << "move operator =     " << &p << " -> " << this << endl;
    return *this;
  }

  C return_local_obj() {
    C c;
    return c;
  }

  C &return_local_obj_ref() {
    C c;
    return c; // never return a local variable reference !!
  }

  C &return_self_obj() { return *this; }

  void accept_obj_as_param(const C c) { this->value = c.value; }

  void accept_obj_ref_as_param(const C &c) { this->value = c.value; }

 private:
  int value;
};

int main() {
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

  return 0;
}
```
程序运行结果：<br>
```
1. constructor only
constructor         0x7ffeac087828
2. copy constructor
copy constructor    0x7ffeac087828 -> 0x7ffeac087820
3. move constructor
move constructor    0x7ffeac087828 -> 0x7ffeac087818
deconstructor       0x7ffeac087818
4. assignment operator
operator =          0x7ffeac087828 -> 0x7ffeac087820
5. function test
::accept_obj_as_param(C)
copy constructor    0x7ffeac087820 -> 0x7ffeac087810
deconstructor       0x7ffeac087810
::accept_obj_ref_as_param(C&)
::return_local_obj()
constructor         0x7ffeac087808
::return_local_obj() v2
constructor         0x7ffeac087800
move operator =     0x7ffeac087800 -> 0x7ffeac087808
deconstructor       0x7ffeac087800
deconstructor       0x7ffeac087808
::return_local_obj_ref()
constructor         0x7ffeac087830
deconstructor       0x7ffeac087830
copy constructor    0x7ffeac087830 -> 0x7ffeac0877f8
::return_local_obj_ref() v2
constructor         0x7ffeac087838
deconstructor       0x7ffeac087838
operator =          0x7ffeac087838 -> 0x7ffeac0877f8
deconstructor       0x7ffeac0877f8
::return_self_obj()
copy constructor    0x7ffeac087828 -> 0x7ffeac0877f0
::return_self_obj() v2
operator =          0x7ffeac087828 -> 0x7ffeac0877f0
deconstructor       0x7ffeac0877f0
end of program
deconstructor       0x7ffeac087820
deconstructor       0x7ffeac087828
```

建议写法:
* <b>函数形参尽量使用常量引用</b>，例如上面的`void C::accept_obj_ref_as_param(const C &c);`；
* <b>切勿返回局部对象的引用</b>，以语句`C c = a.return_local_obj_ref();`为例，程序先构造了一个临时对象`tmp_obj`，然后还没有将临时对象`tmp_obj`的值传给`c`就直接调用析构函数删除了临时对象，因此可能会导致不可预测的结果；
* <b>返回临时对象的最好的方式是直接返回对象而非引用</b>，使用普通变量或右值引用变量获取返回值，此时编译器自动使用返回值优化技术优化掉临时对象，例如上面的`C c = a.return_local_obj();`。
* C++11的右值引用的特性试图实现移动语义和完美转发的功能，其中完美转发功能是由右值引用和引用折叠一起实现的。
