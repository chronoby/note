# Accustoming yourself to C++

## Item 1: view C++ as a federation of languages

- C++是一个多重范性编程语言,同时支持过程形式、面向对象形式、函数形式、泛型形式、元编程形式的语言
- 将C++视为一个由四种相关语言组成的联邦而非单一语言

1. C
2. Object-Oriented C++
3. Template C++: 泛型编程部分
4. STL

## Item 2: prefer consts, enums and inline to #defines

- 当你`#define ASPECT_RATIO 1.653`，记号名称ASPECT_RATIO可能没有进入记号表内。当你运用此常量获得一个编译错误信息，也许会提到1.653而不是ASPECT_RATIO，会引起你或者使用你代码的人的困惑

### 常量

解决办法：`const double AspectRatio = 1.653;`

- 常量定义通常被放在头文件里，有必要将指针声明为const

### class专属常量

- 声明为静态成员变量

```cpp
class GamePlayer {
private:
    static const int NumTurns = 5; // 声明式
    int scores[NumTurns];
}
```

- 此为NumTurns的声明式而非定义式。通常C++要求对你使用的任何东西提供定义式，但如果他是个class专属常量优势static且为整数类型(int, char, bool)，则需特殊处理
- 只要不取它们的地址，或纵使你不去地址而编译器坚持要一个定义式，不虚提供定义式如下：

```cpp
const int GamePlayer::NumTurns; // 定义式
```

- 将这个式子放进实现文件而非头文件
- 由于class常量在声明时已经获得初值，此定义可以不再设初值

### enum hack

- 万一编译器（错误地）不允许static整数型class常量完成in class初值设定，可改用所谓的"the enum hack"做法
- 理论基础是：一个属于枚举类型的数值可以权充int被使用

```cpp
class GamePlayer {
private:
    enum { NumTurns = 5 };
    int scores[NumTurns];
}
```

- 取一个enum的地址不合法。故如果你不想让别人获得一个pointer或reference指向你的某个整数常量，使用enum

## Item 3: use const whenever possible

```cpp
const char* p; // non-const pointer, const data
char* const p; // const pointer, non-const data
```

- const出现在*左边，表示被指物是常量
- const出现在*右边，表示指针自身是常量
- 算术运算符重载返回const类型，防止客户进行如下操作：`(a * b) = c;`

### const 成员函数

基于两个理由：

1. 使class接口容易理解
2. 使操作const对象成为可能。改善C++程序效率的一个根本办法是以 pass by reference-to-const 方式传递对象。而此技术可行的前提是，我们有const成员函数可以用来处理取得的const对象

- 一旦将对象定义为常对象之后，不管是哪种形式，该对象就只能访问被 const 修饰的成员了（包括 const 成员变量和 const 成员函数），因为非 const 成员可能会修改对象的数据（编译器也会这样假设），C++禁止这样做。
- 两个成员函数如果只是常量性不同，可以被重载

例外：

```cpp
class CTextBlock
{
public:
    ...
    char& operator[](std::size_t position) const { return pText[position]; }
private:
    char* pText;
}
```

它允许发生：

```cpp
const CTextBlock cctb("Hello");
char* pc = &cctb[0];
*pc = 'J'; // 没有错误
```

- mutable成员变量：即使在const成员函数内也能被更改

### tips

- 将某些东西生命为const以帮助编译器侦测出错误用法
- 编译器强制实施bitwise constness，但你编写程序时应该使用“概念上的常量性”
- 当const和non-const成员函数有着实质等价的实现时，领non-const版本调用const版本可避免代码重复

## Item 4: make sure that objects are initialized before they're used

- 使用member initialzation list而非在构造函数体内赋值，效率更高
- 总是在初始化列表中列出所有成员变量
