# Deducing types

## Item 2: Understand auto type deduction

模板类型推导和 auto 类型推导有一个直接的映射

```cpp
template <typename T>
void f(ParamType param);

f(expr);
```

- 在调用f的地方，编译器使用expr来推导T和ParamType的类型
- 当一个变量被声明为auto, auto相当于模板中的T，而对变量做的类型限定就是ParamType
- 对 auto 的类型推导只存在一种情况的例外，其他就和模板类型推导完全一样

例外：

如果你想声明一个用27初始化的int，C++ 98 有两种语法选择

```cpp
int x1 = 27;
int x2(27);
```

C++ 11, 通过标准支持的统一初始化，可以

```cpp
int x3 = {27};
int x4{27};
```

使用auto:

```cpp
auto x1 = 27;
auto x2(27);
auto x3 = {27};
auto x4{ 27 };
```

上面的所欲声明都可以编译，但他们和被替换的相应语句意义并不一样

- 前两个是一样的，即声明一个初始化值为27的int
- 后两个声明了一个类型为`std::intializer_list<int>`的变量，这个变量包含了一个唯一的元素27
- 这和 auto 的一种特殊类型推导有关系。当使用一堆花括号来初始化一个auto类型的变量，推导的类型是`std::intializer_list<int>`。如果这种类型无法推导（比如在花括号中变量拥有不同的类型），代码会编译错误

```cpp
auto x5 = { 1, 2, 3.0 }; // 错误！ 不能讲T推导成 std::intializer_list<T>
```

这里实际上是有两种类型推导

- 一是 auto x5 的类型被推导。因为 x5 的初始化是在花括号里面，x5 必须被推导成 std::intializer_list
- std::intializer_list 是一个模板。实例是对一些 T 实例化成 `std::intializer_list<T>` ，这就意味着 T 的类型必须被推导出来。

类型推导就在第二种的推导的范围上失败了。在这个例子中，类型推导失败是因为在花括号里面的数值并不是单一类型的。

相同的初始化传递给相同的模板，类型推导会失败

```cpp
template <typename T>
void f(T param);

f({11, 23, 9}); // 错误
```

但是，如果你明确模板的 param 的类型是一个不知道 T 类型的 `std::initializer_list<T>` :

```cpp
template<typename T>
void f(std::initializer_list<T> initList);
f({ 11, 23, 9 }); // T被推导成int，initList的类型是std::initializer_list<int>
```

auto 和模板类型推导的本质区别就是 auto 假设花括号初始化代表的是std::initializer_list，但是模板类型推导却不是

对于 C++ 14，故事还要继续

C++14允许 auto 表示推导的函数返回值（参看条款3），而且C++14的lambda可能会在参数声明里面使用 auto 。但是，这里面的使用是复用了模板的类型推导，而不是 auto 的类型推导。所以
一个使用 auto 声明的返回值的函数，返回一个花括号初始化就无法编译。

```cpp
auto createInitList()
{
    return { 1, 2, 3 }; // 编译错误：不能推导出{ 1, 2, 3 }的类型
}
```

在C++14的lambda里面，当 auto 用在参数类型声明的时候也是如此：

```cpp
std::vector<int> v;

auto resetV =
[&v](const auto& newValue) { v = newValue; } // C++14

resetV({ 1, 2, 3 }); // 编译错误，不能推导出{ 1, 2, 3 }的类型
```
