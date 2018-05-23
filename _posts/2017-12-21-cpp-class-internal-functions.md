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
本示例演示了调用构造函数、析构函数、拷贝构造函数、赋值函数的时机，这对于我们对内存的控制非常重要。<br>
测试环境说明：
> OS: Linux<br>
> Compiler: clang 3.8.0 <br>
> Options: <b>`-std=c++11`: 开启对移动语义的支持  `-fno-elide-constructors`: 防止移动构造函数被编译器NRVO优化掉</b><br>

### 2. 示例
``` plaintext
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
    return c;  // don't return a local variable reference !!
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
程序运行结果:
``` plaintext
1. constructor only
constructor         0x7ffde81f80b8
2. copy constructor
copy constructor    0x7ffde81f80b8 -> 0x7ffde81f80a8
3. move constructor
move constructor    0x7ffde81f80b8 -> 0x7ffde81f80a0
deconstructor       0x7ffde81f80a0
4. assignment operator
operator =          0x7ffde81f80b8 -> 0x7ffde81f80a8
5. function test
::accept_obj_as_param(C)
copy constructor    0x7ffde81f80a8 -> 0x7ffde81f8098
deconstructor       0x7ffde81f8098
::accept_obj_ref_as_param(C&)
::return_local_obj()
constructor         0x7ffde81f7f30
move constructor    0x7ffde81f7f30 -> 0x7ffde81f8088
deconstructor       0x7ffde81f7f30
move constructor    0x7ffde81f8088 -> 0x7ffde81f8090
deconstructor       0x7ffde81f8088
::return_local_obj() v2
constructor         0x7ffde81f7f30
move constructor    0x7ffde81f7f30 -> 0x7ffde81f8080
deconstructor       0x7ffde81f7f30
move operator =     0x7ffde81f8080 -> 0x7ffde81f8090
deconstructor       0x7ffde81f8080
deconstructor       0x7ffde81f8090
::return_local_obj_ref()
constructor         0x7ffde81f7f30
deconstructor       0x7ffde81f7f30
copy constructor    0x7ffde81f7f30 -> 0x7ffde81f8078
::return_local_obj_ref() v2
constructor         0x7ffde81f7f30
deconstructor       0x7ffde81f7f30
operator =          0x7ffde81f7f30 -> 0x7ffde81f8078
deconstructor       0x7ffde81f8078
::return_self_obj()
copy constructor    0x7ffde81f80b8 -> 0x7ffde81f8070
::return_self_obj() v2
operator =          0x7ffde81f80b8 -> 0x7ffde81f8070
deconstructor       0x7ffde81f8070
end of program
deconstructor       0x7ffde81f80a8
deconstructor       0x7ffde81f80b8
```
总结:
* 函数形参尽量用引用，例如上面的`void C::accept_obj_ref_as_param(const C &c)`；
* <b>函数可以返回普通的局部对象，但是切勿返回局部对象的引用</b>，以`C c = a.return_local_obj_ref()`为例，程序先通过构造函数构造了一个临时对象`tmp_obj`，然后还没有将临时对象`tmp_obj`的值传给`c`就直接调用析构函数删除了临时对象，这可能会导致不可预知的后果；
* C++11的右值引用的特性试图实现移动语义和完美转发的功能，其中完美转发功能是由右值引用和引用折叠一起实现的。
