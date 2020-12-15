# Notes-CPlusPlus-HouJie
学习笔记《C++面向对象高级编程》- 侯捷

### 头文件与类的声明

- Header (頭文件) 中的防衛式聲明
```cpp
#ifndef __COMPLEX__
#define __COMPLEX__
...
#endif
```

### 构造函数

- 构造函数的写法：initialization list
```cpp
complex (double r = 0, double i = 0)
: re (r), im (i)
{ }
```

- 构造函数放在private区
  - 不允许被外界实例化对象
  - 设计模式Singleton单例模式
```cpp
class A {
  public:
  static A& getInstance();
  setup() { ... }
private:
  A();
  A(const A& rhs);
...
};
A& A::getInstance() {
  static A a;
  return a;
}
A::getInstance().setup();
```

### 成员函数

- 声明：不改变成员变量的成员函数一定要加const，否则使用时可能会报错。
```cpp
double real () const { return re; }
```

- 返回值：返回局部变量不可以返回引用，其余情况都尽量返回引用。


### 友元

- 相同class 的各個objects 互為friends (友元)
```cpp
int complex::func(const complex& param) { return param.re + param.im; }

complex c1(2,1);
complex c2;
c2.func(c1); 
```

### 操作符重载

- 操作符有两种写法
  - 成员函数写法
  - 全局函数写法
