---
layout: post
title:  "Effective C++总结"
date:   2022-11-21 08:30:00 +0800
categories: [Tech]
excerpt: 非C++入门；总结自《Effective C++》第3版中文版
tags:
  - CN
  - C++ 
  - Effective C++ (3th Edition)
  - 
---
<!-- 正如这本书的名字《高效C++》，这本书的目的在于提高C++编程能力，而不是入门C++。如果你刚接触C++，别的书更适合，例如《快速C++》(accelerated C++)。 -->



# <center>一、习惯C++

# <center>accustoming yourself to C++

《Effective C++》的第一部分，理解C++

<br /> 
#### 01.视C++为一个语言联邦
#### view C++ as a federation of languages
C++是一个夹杂很多种特性的语言。如果把C++视为一个由4种编程语言组成的联邦，针对每个次语言(sublanguage)，它的各种特性就很清晰易懂了。

C
>区块(block)、语句(statement)、预处理(preprocessor)、内置数据类型(built-in data type)、数组(array)、指针(pointer)等来自C

Object-Oriented C++
>类(class)，封装(encapsulation)，继承(inheritance)，多态(polymorphism)，虚函数(visual function)等

Template C++
> 泛型编程(generic programming)。由此产生了新的编程范型(programming paradigm)，即模板元编程(template metaprogramming, TMP)

STL(standard template library)
>容器(container)，迭代器(iterator)，算法(algorithm)以及函数对象(function object)

对于同一次语言，所遵守的规则和实现高效的方式大致相同，对于不同次语言，所遵守的规则和实现高效的方式各不相同。因此，在编程时，应时刻注意到自己在使用C++的哪一个次语言，然后充分利用这一次语言的特性来实现准确和高效。

<br /> 
#### 02.尽量用consts,enums 和 inlines替换#defines
#### prefer consts,enums and inlines to #defines

<font color="green">对于单纯常量，最好用const对象或enums替换#define</font>
```
#define PI 3.14
```
PI 可能在预处理器 <font color="#dd0000">???</font><br /> 

一旦用`#defines`定义了宏，就在编译中一直有效(除非被#undef)。这意味着没有提供封装性。

在class编译期需要使用一个常量值时，#define可以定义该常量。另一种做法是把这个常量用枚举表示，即`the enum hack`，其原理是“一个属于枚举类型(enumerated type)的数值可当作`int`使用”。例如如下代码
```
class Game
{
private:
  enum { Num = 5 }; // “the enum hack”——令`Num`成为5的一个记号名称
  int score[Num];
};
```
事实上，enum hack是 template metaprogramming 的基础技术。

---
<font color="green">使用 "template inline函数" 代替 "用#define实现宏(macros)"</font>
```
#define MAX(a,b) f((a) > (b) ? (a) : (b))
int a = 5, b = 10;
MAX(++a, b);    // a在判断和选择值的过程中各累加一次
MAX(++a, b+10); // a在判断过程中累加一次
```
以上用法的结果是不可预料的。而使用template inline函数可获得宏带来的效率以及可预料的行为和类型安全性(type safety)
```
template<typename T>
inline void Max(const T& a, const T& b)
{
  f(a > b ? a : b);
}
```
<br /> 
#### 03.尽量用const
#### use const whenever possible

```
char greeting[] = "hello";
char* p = greeting;             // 指针和数据都不是常量
const char* p = greeting;       // 数据是常量
char* const p = greeting;       // 指针是常量
const char* const p = greeting; // 指针和数据都是常量
```
const 在 * 左侧，数据是常量；const 在 * 右侧，指针是常量。和类型名称的位置无关。<br /> 

---
STL的迭代器也有类似用法
```
const std::vector<int>::iterator iter = vec.begin(); // iter不能变，所指向的数据可以变
const_iterator std::vector<int>::iterator iter = vec.begin(); // iter指向的数据不能变
```

---
const成员函数
之后的内容已经不明白其作用了，暂时跳过<font color="#dd0000">???</font><br /> 
两个函数只是常量性(constness)不同，也可以被重载。<br /> 
bitwise(physical) constness和logical constness。<br /> 
常量性转除(casting away constness)只能由const_cast完成。<br /> 
应该避免用const版本调用non-const 版本。<br /> 
编译器强制实施bitwise constness，但编写程序时应使用“概念上的常量性”(conceptual constness)。 <br /> 
当const和non-const成员函数有着实质等价的实现时，令non-const版本调用const版本可避免代码重复<br /> 


<br /> 
#### 04.确定对象被使用前已初始化
#### make sure that objects are initialized before they're used

构造函数的执行顺序：
>按顺序执行编写的成员初始化 --> 按顺序初始化剩余的成员变量 --> 进入函数体

有两种编写成员初始化的方法：
使用成员初值列完成初始化的代码示例如下：
```
Entry::Entry(int a)
: am(a), bm(0) // am,bm 是int型的成员变量
{}
```
另外一种是在声明时初始化，代码示例如下：
```
class Entry
{
 public:
  const int a = 10;
};
```

也就是说成员变量在进入构造函数的函数体之前就已经完成初始化，这个初始化是“成员初值列”(member intialization list)。在函数体内的操作是赋值(assignment)而不是初始化。
如果成员变量是const或reference，则一定需要初值，不能被赋值。

初始化次序大致固定：先初始化基类(base class)，再初始化派生类(derived class)；类的成员变量总是以声明次序被初始化。建议 在编写初始化列时 按照声明次序条列成员。


编译单元(translation unit) 是指产出单一目标文件(single object file)的那些源码。基本上它是单一源码文件加上其所含入的头文件(#include files)。

如果某编译单元内的某个non-local static 对象的初始化使用了另一编译单元内的某个 non-local static 对象，它用到的这个对象可能未初始化，因为C++对“定义于不同编译单元内的 non-local static 对象”的初始化顺序无明确定义。但可以使用某些方式确定不同编译单元的的依赖关系和编译顺序，例如使用CMake。<br />
```
class Base
{
 public:
  Base& contruct()
  {
    static Base base;
  }
}
```
在以上代码中，在成员函数`contruct`内声明一个`Base`的 static 对象，并返回一个指向该对象的引用(reference)，然后用户调用这个成员函数，而不直接操作该对象。这是单例(Singleton)模式的一个常见实现手法。

这种方式在多线程系统种带有不确定性，其中一个解决办法是，在程序的单线程启动阶段(single-threaded startup portion)手动调用 reference-returning 函数。
<br /> <br /> 

---

<br /> 


# <center>二、构造、析构和赋值运算

# <center>constructor,deconstructor, assignment operators

《Effective C++》的第二部分。

<br /> 
#### 05.了解C++默认调用了哪些函数
#### what functions C++ silently writes and calls
如果自己没有声明，编译器会声明一个 default 构造函数、 copy 构造函数、 copy assignment 操作符和一个析构函数，这些函数都是 public 且 inline。编译器给出的析构函数是一个non-virtual 函数。

对于内含 reference 成员”的 class ，直接赋值意味着 reference 改指向不同对象，这是C++不允许的，无法通过编译。因此，如果在“内含 reference 成员”的 class 内支持赋值操作(assignment)，必须自己定义 copy assignment。

<br /> 
#### 06.当不适用编译器生成的函数时，应明确写出
#### explicitly disallow the use of complier-generated functions you do not want

如果不希望类具有某一功能，比如禁用 copy 构造函数和 copy assignment 操作符。因为编译器产出的函数都是 public，将 copy 构造函数和 copy assignment 操作符声明为 private可以防止编译器生成相关函数。如果这些函数仅声明没有实现，当用户试图复制改对象时，编译器会报相关函数未定义的错误。如果在成员函数和友元函数中复制，那么连接器会报出错误。这样就禁用了类的复制功能。

在基类中把 copy 构造函数和 copy assignment 操作符声明为 private ，那么即使在派生类的成员函数和友元函数中复制，编译器也会报错。这种方式提前了错误发现的时间。

<br /> 
#### 07.为多态基类声明虚析构函数
#### declare destructor virtual in polymorphic base classes
 
当派生类经由一个基类指针被删除，而该基类带着一个非虚析构函数，实际执行时通常是派生类的成分没有销毁。如果基类为虚析构函数，那么删除派生类的对象时就睡调用派生类的析构函数。

任何一个类只要带有虚函数，很可能会用作基类，那么就应该有一个虚析构函数。<br /> 

为了在运行期决定哪一个虚函数被调用，对象必须存储相关的信息，该信息由 vptr(virtual table pointer) 指针指出。vptr 指向一个由函数指针构成的数组，称为 虚表(virtual table, vtbl)；每个有虚函数的类都有一个虚表。这意味着，有虚函数的对象必须占用更多的空间存储指针。<br /> 

一个类如果不会用作基类，那么使用虚函数会增加对象的存储空间却毫无用处。而该对象也不再和其它语言(如 C)内的相同声明具有相同结构，因此不再具有移植性。

标准string不含任何虚函数，含有非虚析构函数。因此不能继承。

<br /> 
#### 08.为多态基类声明虚析构函数
#### prevent exception from leaving destructors

```
~Entry()
{
  ... // (1)
  if(maybe())
  {
    throw(); // 抛出异常
  }
  ... // (2)
}
```
观察以上析构函数，如果抛出异常，意味着 (2) 的程序将不会再执行。如果(2)中执行的是一些成员的析构，那就意味着本该再(2)中析构的成员没有析构，从而产生了内存泄露。

有两个办法避免这一问题。<br /> 

1.在析构中出现异常强制结束程序
```
~Entry()
{
  ... // (1)
  if(maybe())
  {
    std::abort(); // 强制结束程序
  }
  ... // (2)
}
```
2.吞下异常
```
~Entry()
{
  ... // (1)
  try {}
  catch(...)
  {
    // 制作转运记录，记下该异常
  }
  ... // (2)
}
```

另外的办法，则是把这段可能出现异常的程序移出析构函数，在执行析构函数前单独执行，由客户对该异常作出反应。

<br /> 
#### 09.不要再构造和析构过程中调用虚函数
#### never call virtual functions during construction or destruction
```
class Base
{
  public:
  Base(std::string sensorID)
  {
    readParam(sensorID);
  }
  virtual readParam(std::string sensorID)
  {
    ... // 读取默认参数
  }
}
class Derived: public Base
{
  public:
  Derived(std::string lidarID)
  {
    ...
  }
  readParam(std::string lidarID)
  {
    ... // 读取雷达参数
  }
}
```
在这个例子中，基类的虚函数是读取默认传感器参数的，派生类重写的函数是读取激光雷达的参数。构造派生类对象时，先调用基类的构造函数，然后再调用派生类的构造函数。基类构造函数中使用了虚函数，调用的是基类自己的虚函数，而不是派生类中重写后的函数。而我本想使用的是重写后的函数，因此这种写法是有问题的。对于析构函数也是如此。

这种情景的特点是，基类成分在初始化的过程中需要用到派生类的属性。那么一种解决办法就是把该属性作为参数传入基类的构造函数。看以下代码，利用辅助函数把派生类对应的传感器类型传入基类的构造函数。我在此处省去了在基类中如何修改的代码。
```
class Derived: public Base
{
  public:
  Derived(std::string lidarID)
    : Base(sensorType(lidarID)) // sensorType(lidarID) 为一个可以返回传感器类型的函数
  {
    ...
  }
  readParam(std::string lidarID)
  {
    ... // 读取雷达参数
  }
}
```
<br /> 
#### 10.令 operator= 返回一个指向 *this 的引用
#### have assignment operators return a reference to *this

这一条包括 `operator+=` `operator-=` `operator*=`等等。虽然这一条并不具有强制性，但考虑到内置类型和标准程序库提供的类型如 string vector complex 等都遵守这一做法，还是尽量遵守比较好。

<br /> 
#### 11.在 operator= 中处理“自我赋值”
#### handle assignment to self in operator=

```
*px = *py; // 潜在的自我赋值
```
别名(aliasing): 有一个以上的方法指向某个对象。当代码中操作 pointers 或 reference 时，就有可能出现不同变量名指向同一个对象。防止这种情况的传统做法是在 operator= 的函数中先进行“证同测试”(identity test)。
```
Widget& Widget::operator=(const Widget& rhs)
{
  if(this == &rhs) // 证同测试
  {
    return *this; 
  }
  ... // 其它程序
}
```
<br /> 
#### 12.复制对象时要复制每一个成分
#### copy all parts of an object

手动写的复制函数和 operator= 存在忘记复制某个成员的可能，而且编译器不会提示此类错误。
考虑到派生类不能访问基类中的 private 成员，因此为派生类写复制函数时，应该让派生类的复制函数调用基类中的相应函数。

复制构造函数和 复制赋值(copy assignment)操作符 之间不应该有调用关系。如果两者有相同的代码块，应该用这个相同的代码块建立新的成员函数并调用。
<br /> <br /> 

---

<br /> 

# <center>三、资源管理

# <center>resource management

常见的资源有内存、文件描述器(file descriptors)、互斥锁(mutex locks)、图形界面中的字型和笔刷、数据库连接、网络 sockets。管理资源的一个重点在于，当不再需要某个资源时，确保该资源被释放。

<br /> 
#### 13.用对象管理资源
#### use objects to management resources

“以对象管理资源” 也成为 “资源获取即初始化” (resource acquisition is initialization, RAII)。在获得资源后就立即放入对象中，可能时初始化某个对象或是为某个对象赋值。这样，对应的资源会在析构函数中释放，从而防止资源泄漏。<br />

两个常用的 RAII 类是 auto_ptrs 和 shared_ptr。<br />

auto_ptrs 是个 “ 类指针(pointer-like)对象”,其析构函数自动对指向的对象调用 delete。如果通过 复制构造函数 或 复制赋值(copy assignment)操作符 复制它，它会变为 null。 而复制得到的指针将取得资源的唯一拥有权。<br />

shared_ptr 是一种“引用计数型智能指针”(reference-counting smart pointer, RCSP)，能计算共有多少对象指向某个资源，并在无人指向它时自动删除该资源。但它无法打破`环状引用`(cycles of reference)。



<br /> 
#### 14.注意在资源管理类中复制行为
#### care about copying behavior in resource-managing calsses

为保证不会忘记把一个锁住的 Mutex 解锁，可以建立一个 class 用来管理锁。
```
class Lock
{
public:
  explicit Lock(Mutex* pm)
    : mutexPtr(pm)
  {
    lock(mutexPtr); // 获得资源
  }
  ~Lock() 
  {
    unlock(mutexPtr); // 释放资源
  }
private:
  Mutex *mutexPtr;
};
```

当运行如下复制代码时，可能会导致未知的风险。
```
Mutex m;
Lock m1(&m); // 锁定 m 
Lock m2(m1); // 
```
此时有两种选择。<br />

**禁止复制**：例如把复制操作声明为 private。<br />

**引用计数法(reference-count)**：通常使用 share_ptr 来实现这一特性。<br />
当用于 mutex 时，在引用次数为 0 时，我们希望的动作是解除锁定，而 share_ptr 的默认动作是删除 mutex. 
此时，可以为 share_ptr 的对象指定删除器(deleter)，当该对象的引用次数为 0 时调用该删除器。示例代码如下：
```
class Lock
{
public:
  explicit Lock(Mutex* pm)
    : mutexPtr(pm, unlock) // 把 unlock 作为删除器
  {
    lock(mutexPtr.get()); // 获得资源
  }
private:
  std::share_ptr<Mutex>  mutexPtr;
};
```
**复制底部资源**：复制资源时，不论指针还是指针所指的内容都会被制作出一个复件，即 `深拷贝`(deep copying).<br />

**转移底部资源的所有权**：如果要求 指向该内容的 永远只有一个对象，可以用 auto_ptrs 实现。

<br /> 
#### 15.在资源管理类中提供对原始资源的访问
#### provide access to raw resource in resource-managing classes

考虑以下这个用于字体的 RAII class
```
FontHandle getFont(); 
void releaseFont(FontHandle fh);
class Font
{
 public:
  explict Font(FontHandle fh) // 获得资源
    : f(fh) // pass-by-value
  { }
  ~Font() { releaseFont(f); } // 释放资源
  FontHandle get() const { return f; }      // 显式转换函数
  operator FontHandle() const { return f; } // 隐式转换函数
 private:
  FontHandle f; // 原始字体资源
}
```