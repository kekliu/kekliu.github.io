---
layout:     post
title:      "C++ 类型和值类别"
subtitle:   ""
date:       2017-12-23 14:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - C++
---


### 1. Type
```
The C++ type system consists of the following types:

fundamental types (see also std::is_fundamental):
* the type void (see also std::is_void);
* the type std::nullptr_t (since C++11) (see also std::is_null_pointer);
* arithmetic types (see also std::is_arithmetic):
  * floating-point types (float, double, long double) (see also std::is_floating_point);
  * integral types (see also std::is_integral):
    * the type bool;
    * character types:
      * narrow character types (char, signed char, unsigned char);
      * wide character types (char16_t, char32_t, wchar_t);
    * signed integer types (short int, int, long int, long long int);
    * unsigned integer types (unsigned short int, unsigned int, unsigned long int, unsigned long long int);
* compound types (see also std::is_compound):
  * reference types (see also std::is_reference):
    * lvalue reference types (see also std::is_lvalue_reference):
      * lvalue reference to object types;
      * lvalue reference to function types;
    * rvalue reference types (see also std::is_rvalue_reference):
      * rvalue reference to object types;
      * rvalue reference to function types;
  * pointer types (see also std::is_pointer):
    * pointer to object types;
    * pointer to function types;
  * pointer to member types (see also std::is_member_pointer):
    * pointer to data member types (see also std::is_member_object_pointer);
    * pointer to member function types (see also std::is_member_function_pointer);
  * array types (see also std::is_array);
  * function types (see also std::is_function);
  * enumeration types (see also std::is_enum);
  * class types:
    * non-union types (see also std::is_class);
    * union types (see also std::is_union).

For every type other than reference and function, the type system supports three additional cv-qualified versions of that type (const, volatile, and const volatile).

Types are grouped in various categories based on their properties:
* object type is a (possibly cv-qualified) type that is not a function type, not a reference type, and not void type (see also std::is_object);
* scalar types are (possibly cv-qualified) arithmetic, pointer, pointer to member, enumeration, and std::nullptr_t types (see also std::is_scalar);
* trivial types (see also std::is_trivial), POD types (see also std::is_pod), literal types (see also std::is_literal_type), and other categories listed in the the type traits library or as named type requirements.
```

### 2. Value categories
#### 2.1 What is glvalue/prvalue/xvalue/lvalue/rvalue
```
* a glvalue is an expression whose evaluation determines the identity of an object, bit-field, or function;
* a prvalue is an expression whose evaluation either
  * computes the value of the operand of an operator (such prvalue has no result object), or
  * initializes an object or a bit-field (such prvalue is said to have a result object). All class and array prvalues have a result object even if it is discarded. In certain contexts, temporary materialization occurs to create a temporary as the result object;
* an xvalue is a glvalue that denotes an object or bit-field whose resources can be reused;
* an lvalue is a glvalue that is not an xvalue;
* an rvalue is a prvalue or an xvalue.
```
<b>glvalue and rvalue are mixed categories</b>
#### 2.2 Value categories in C++11
```
With the introduction of move semantics in C++11, value categories were redefined to characterize two independent properties of expressions[5]:
* has identity: it's possible to determine whether the expression refers to the same entity as another expression, such as by comparing addresses of the objects or the functions they identify (obtained directly or indirectly);
* can be moved from: move constructor, move assignment operator, or another function overload that implements move semantics can bind to the expression.

In C++11, expressions that:
* have identity and cannot be moved from are called lvalue expressions;
* have identity and can be moved from are called xvalue expressions;
* do not have identity and can be moved from are called prvalue ("pure rvalue") expressions;
* do not have identity and cannot be moved from are not used[6].

The expressions that have identity are called "glvalue expressions" (glvalue stands for "generalized lvalue"). Both lvalues and xvalues are glvalue expressions.
The expressions that can be moved from are called "rvalue expressions". Both prvalues and xvalues are rvalue expressions.
```
