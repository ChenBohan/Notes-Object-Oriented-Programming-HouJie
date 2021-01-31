# Notes-Object-Oriented-Programming-HouJie
学习笔记《C++面向对象高级编程》- 侯捷

### 2.头文件与类的声明

- C vs C++
  - 面向对象：C++把数据和处理数据的函数封装在一起作为class

- Class的分类
  - 带指针的（string类）
  - 不带指针的（complex类）

- Header (頭文件) 中的防衛式聲明
  - 避免重复include
  - 使用者不需要注意include的顺序
```cpp
#ifndef __COMPLEX__
#define __COMPLEX__
...
#endif
```

- 头文件的布局
  - 前置声明
  - 类的声明
  - 类的定义

### 3.构造函数

- inline
  - inline函数效率会比较高
  - inline关键字只是对编译器的建议

- 构造函数好的写法：initialization list
  - 变量的生成有两个阶段
    - 1.初始化，冒号后
    - 2.赋值：大括号里
```cpp
complex (double r = 0, double i = 0)
  : re (r), im (i) { }
```

### 4.参数传递与返回值

- 设计模式Singleton单例模式
  - 构造函数放在private区
    - 不允许被外界实例化对象
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

A& A::getInstance()
{
  static A a; // 自己创建了一份自己，且只有一份
  return a;
}

A::getInstance().setup(); // 外界调用时要靠getInstance()取得唯一的一个实例
```

- const成员函数
  - 好习惯：不改变成员变量的成员函数一定要加const，否则使用时可能会报错。
```cpp
const complex c1(1, 2);
cout << c1.real();  // 若complex::real()没加const，const对象调用非const函数，会报错
cout << c1.imag();
```

- 参数传递
  - 好习惯：所有参数传递都传引用

- 返回值传递
  - 好习惯：返回局部变量不可以返回引用，其余情况都尽量返回引用。


- 友元
  - 友元函数可以自由取得类的private成员
  - 相同class的不同成员互为友元
```cpp
class comple
{
public:
  int complex::func(const complex& param)
  { return param.re + param.im; }
  ...
}
c2.func(c1); 
```

### 5.操作符重载

- 操作符重载有两种写法（任何操作都有两种写法）
  - 成员函数写法（内含this pointer）
```cpp
inline complex& complex::opereator += (const complex& r) {
  return __doapl (this, r); // 内含this pointer
inline complex& __doapl (complex* ths, const complex& r) {
  ths->rm += r.rm;
  ths->im += r.im;
  return *ths;
}
c2 += c1;
```
  - 非成员函数写法（全局函数）写法（无this pointer）
```cpp
inline complex operator + (const complex& x, const complex& y) {
  return complex (real(x) + real(y), imag(x) + imag(y));  // 返回local object，不能return by reference
}
```

- 良好的class的编程习惯
  - 构造函数会用initialization list
  - 该加const的成员函数要加const
  - 参数传递尽量pass by reference
  - 返回值尽量return by reference
  - 数据放在private区

### 7.三大函数：拷贝构造，拷贝赋值，析构函数

- 编译器默认三大函数
  - 在带指针的类中，使用默认三大函数会有问题
  
- String类
```cpp
class String
{
public:
  String(const char* cstr = 0);
  String(const String& str);
  String& operator=(const String& str);
  ~String();
  inline char* get_c_str() const { return m_data; }
private
  char* m_data; // 动态分配内存
```

- 构造函数
```cpp
inline String::String(const char* cstr = 0)
{
  if (cstr) {
    m_data = new char[strlen(cstr) + 1];
    strcpy(m_data, cstr);
  } else {
    m_data = new char[1];
    *m_data = '\0';
}
```

- 析构函数
```cpp
inline ~String()
{
  delete[] m_data;  // 释放动态分配的内存，否则内存泄漏
}
```

- 拷贝构造函数
```cpp
inline String::String(const String& str)
{
  m_data = new char[strlen(str.m_data) + 1];
  strcpy(m_data, str.m_data);
}
```
  
- 拷贝赋值函数
  - 与拷贝构造函数的区别：拷贝赋值时，这个对象已经存在，所以需要先清空再拷贝
```cpp
inline String::String operator= (const String& str)
{
  if (this == &str) // 检测自我赋值，否则自我复制会出错
    return *this; 
  delete[] m_data;
  m_data = new char[ strlen(str.m_data) + 1];
  strcpy(m_data, str.m_data);
  return *this;
```

### 8.堆、栈与内存管理

- 变量的生命期
  - stack object (local object, auto object)：离开作用域会被自动清理
  - static object：在程序结束时才结束
  - global object：在程序结束时才结束
  - heap object: 在被delete之后才结束

- new先分配内存，再调用构造函数
  - 1.分配内存：内部调用malloc(n)`void* mem  = operator new(sizeof(Complex));
  - 2.转型 `pc = static_cast<Complex*>(mem);`
  - 3.构造函数 `pc->Complex::Complex(1,2);`

- delete先调用析构函数，再释放内存
  - 1.析构函数 `String::~String(ps)`
  - 2.释放内存：内部调用free(ps) `operator delete(ps)`
  
- array new一定要搭配array delete
```cpp
String* p = new String[3];
...
delete[] p;
```

### 10.补充：类模板，函数模板及其他

- static
  - static成员变量：与对象脱离，在内存中只有一份
    - 使用场景：与对象无关的一般数据
    - 要在类的外部赋值：`double Account::m_rate = 8.0;`
  - non static成员函数：在内存中只有一份，靠this pointer来区分要处理哪个对象 `double real() const { return this->re; }`
  - static成员函数：没有this pointer，只能处理static成员变量
    - 调用static函数的方式
      - 1.通过对象调用 `a.set_rate(7,0);`
      - 2.通过class name调用 `Account::set_rate(5.0);`
