# Item 15: Use constexpr whenever possible

## constexpr

constexpr 表明一个值不仅仅是常量，还是编译期可知的。编译期可知的值享有特权，他们可能被放到只读存储空间中。

```cpp
int sz;
constexpr auto arraySize1 = sz;         // error! sz的值在编译期不可知
std::array<int, sz> data1;              // error!
constexpr auto arraySize2 = 10;
std::array<int, arraySize2> data2;      // fine
```

const 不提供 constexpr 所能保证的事，因为const对象不需要在编译期初始化它的值

```cpp
int sz;
const auto arraySize = sz;
std::array<int, arraySize> data;  // error! arraySize的值在编译期不可知
```

简而言之，所有constexpr对象都是const，但不是所有const对象都是constexpr

## 涉及函数的constexpr

如果实参是编译期常量，它们将产出编译期值；如果是运行时值，他们将产出运行时值

- constexpr函数可以用于需求编译期常量的上下文。如果你传给constexpr函数的实参在编译期可知，那么结果将在编译期计算。如果实参的值在编译期不知道，你的代码就会被拒绝。
- 当一个constexpr函数被一个或者多个编译期不可知值调用时，它就像普通函数一样，运行时计算它的结果。这意味着你不需要两个函数，一个用于编译期计算，一个用于运行时计算。constexpr全做了。

## example

例：我们需要编译器计算pow

- C++ 11 中，constexpr函数的代码不超过一行豫剧：一个return

```cpp
constexpr
int pow(int base, int exp) noexcept
{
    return (exp == 0 ? 1 : base * pow(base, exp - 1));
}
constexpr auto numConds = 5;
std::array<int, pow(3, numConds)> results;
```

- C++ 14 中，constexpr函数的限制变得非常宽松

```cpp
constexpr
int pow(int base, int exp) noexcept
{
    auto result = 1;
    for(int i = 0; i < exp; i++)
        result *= base;
    return result;
}
```

- 在C++ 11中，除了void外的所有内置类型外还包括一些用户定义的量可以是字面值，因为构造函数和其他成员函数可以是constexpr

```cpp
class Point {
public:
	constexpr Point(double xVal = 0, double yVal = 0) noexcept : x(xVal), y(yVal)
	{}
	constexpr double xValue() const noexcept { return x; } 
	constexpr double yValue() const noexcept { return y; }
	
	void setX(double newX) noexcept { x = newX; }
    void setY(double newY) noexcept { y = newY; }
private:
	double x, y;
};
```

Point的构造函数被声明为constexpr，因为如果传入的参数在编译期可知，Point的数据成员也能在编译器可知。因此Point就能被初始化为constexpr：

```cpp
constexpr Point p1(9.4, 27.7); // 没问题，构造函数会在编译期“运行”
constexpr Point p2(28.8, 5.3); // 也没问题
```

类似的，xValue和yValue的getter函数也能是constexpr，因为如果对一个编译期已知的Point对象调用getter，数据成员x和y的值也能在编译期知道。这使得我们可以写一个constexpr函数里面调用Point的getter并初始化constexpr的对象：

```cpp
constexpr
Point midpoint(const Point& p1, const Point& p2) noexcept
{
    return { (p1.xValue() + p2.xValue()) / 2, (p1.yValue() + p2.yValue()) / 2 };
}
constexpr auto mid = midpoint(p1, p2);
```

这意味着mid对象通过调用构造函数，getter和成员函数就能在只读内存中创建！它也意味着你可以在模板或者需要枚举量的表达式里面使用像mid.xValue()*10的表达式！它也意味着以前相对严格的某一行代码只能用于编译期，某一行代码只能用于运行时的界限变得模糊，一些运行时的普通计算能并入编译时。越多这样的代码并入，你的程序就越快。（当然，编译会花费更长时间）

在C++11中，有两个限制使得Point的成员函数setX和setY不能声明为constexpr。第一，它们修改它们操作的对象的状态， 并且在C++11中，constexpr成员函数是隐式的const。第二，它们只能有void返回类型，void类型不是C++11中的字面值类型。这两个限制在C++14中放开了，所以C++14中Point的setter也能声明为constexpr：

```cpp
class Point {
public:
	...
	constexpr void setX(double newX) noexcept { x = newX; }
	constexpr void setY(double newY) noexcept { y = newY; }
	...
};
```

现在也能写这样的函数：

```cpp
constexpr Point reflection(const Point& p) noexcept
{
	Point result; 
	result.setX(-p.xValue());
	result.setY(-p.yValue()); 
	return result;
}
```

用户代码可以这样写：

```cpp
constexpr Point p1(9.4, 27.7);
constexpr Point p2(28.8, 5.3);
constexpr auto mid = midpoint(p1, p2);

constexpr auto reflectedMid = reflection(mid);    // reflectedMid的值在编译期可知                                
```

本章的建议是尽可能的使用constexpr，现在我希望大家已经明白缘由：constexopr对象和constexpr函数可以用于很多非constexpr不能使用的场景。使用constexpr关键字可以最大化你的对象和函数可以使用的场景。

还有个重要的需要注意的是constexpr是对象和函数接口的一部分。加上constexpr相当于宣称“我能在C++要求常量表达式的地方使用它”。如果你声明一个对象或者函数是constexpr，客户端程序员就会在那些场景中使用它。如果你后面认为使用constexpr是一个错误并想移除它，你可能造成大量客户端代码不能编译。尽可能的使用constexpr表示你需要长期坚持对某个对象或者函数施加这种限制。
