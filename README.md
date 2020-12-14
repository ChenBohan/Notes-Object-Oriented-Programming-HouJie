# Notes-CPlusPlus-HouJie
学习笔记《C++面向对象高级编程》- 侯捷

#### 头文件与类的声明

- Header (頭文件) 中的防衛式聲明
```cpp
#ifndef __COMPLEX__
#define __COMPLEX__
...
#endif
```

#### 构造函数

- 构造函数的写法：initialization list
```cpp
complex (double r = 0, double i = 0)
: re (r), im (i)
{ }
```

- 构造函数放在private区
  - 不允许被外界实例化对象
  - 设计模式Singleton单例模式
  - 里面自己

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
