# Deducing types

## Item 1: Understand template type deduction

函数模板：

```cpp
template<typename T>
void f(ParamType param);
```

调用会是这样：

```cpp
f(expr); // 调用f
```

在编译的时候，编译器通过 expr 来进行推导出两个类型：一个是 T 的，另一个
是 ParamType 。通常来说这些类型是不同的，因为 ParamType 通常包含一些类型的修饰，比如 const 或引用特性

e.g.

```cpp
template <typename T>
void f(const T& param);
```

调用:

```cpp
int x = 0；
f(x);
```

T被推导为int，ParamType被推导为const int&

---

- T 的类型不仅和 expr 的类型独立，而且还和 ParamType 的形式独立。

下面是三个例子：

- ParamType 是一个指针或者是一个引用类型，但并不是一个通用的引用类型（通用的引
用类型的内容在条款24。此时，你要知道例外情况会出现的，他们的类型并不和左值应用或者右值引用）。
- ParamType 是一个通用的引用
- ParamType 既不是指针也不是引用

### ParamType 是个非通用的引用或者是一个指针

类型推导过程如下：

1. 如果expr的类型是个引用，忽略引用的部分
2. 利用expr的类型和ParamType对比去判断T的类型

e.g. 函数模板如下：

```cpp
template<typename T>
void f(T & param);
```

我们有这样的代码变量声明

```cpp
int x = 0;
const int cx = x;
const int& rx = x;
```

在如下调用中推导如下：

```cpp
f(x);  // T是int, ParamType是int &
f(cx); // T是const int, ParamType是const int &
f(rx); // T是const int, ParamType是const int &
```

- 如果 param 是一个指针，情况也类似

### ParamType 是个通用的引用（Universal Reference）

通用的引用参数：

- 如果 expr 是一个左值，T 和 ParamType 都会被推导成左值引用
- 如果 expr 是一个右值，则执行普通规则（第一种情况）

```cpp
template<typename T>
void f(T&& param); // param现在是一个通用的引用
int x = 27;       // 和之前一样
const int cx = x; // 和之前一样
const int& rx = x;// 和之前一样
f(x);   // x是左值，所以T是int&
        // param的类型也是int&
f(cx);  // cx是左值，所以T是const int&
        // param的类型也是const int&
f(rx);  // rx是左值，所以T是const int&
        // param的类型也是const int&
f(27);  // 27是右值，所以T是int
        // 所以param的类型是int&&
```

### ParamType 既不是指针也不是引用

当 ParamType 既不是指针也不是引用，我们把它处理成pass-by-value

- 如果 expr 的类型是个引用，忽略引用的部分
- 忽略 expr 的引用特性（忽略const, volatile）

```cpp
template<typename T>
void f(T param); // param现在是pass-by-value
int x = 27;      // 和之前一样
const int cx = x;  // 和之前一样
const int& rx = x; // 和之前一样
f(x);  // T和param的类型都是int
f(cx); // T和param的类型也都是int
f(rx); // T和param的类型还都是int
```

考虑指向 const 对象的 const 指针

```cpp
template<typename T>
void f(T param); // param仍然是按值传递的（pass by value）
const char* const ptr = "Fun with pointers"; // ptr是一个const指针，指向一个const对象
f(ptr);
```

- ptr 的 const 特性被忽略
- ParamType为 const char *

### 数组参数

```cpp
template<typename T>
void f(T param);

const char name[] = "Bye, c++!"
f(name); // T 被推导成const char*
```

特例：

```cpp
template<typename T>
void f(T& param)

f(name);
```

- T 被推导成 const char [13]
- ParamType 被推导成`const char(&)[13]`

声明数组的引用可以创造出一个推导出一个数组包含的元素长度的模板

```cpp
template <typename T, std::size_t N>
constexpr std::size_t arraySize(T (&)[N]) noexcept
{
    return N;
}
```

### 函数参数

函数类型可以退化成函数指针

```cpp
void someFunc(int， double); // someFunc是一个函数
                             // 类型是void(int, double)
template<typename T>
void f1(T param); // 在f1中 参数直接按值传递
template<typename T>
void f2(T& param); // 在f2中 参数是按照引用传递
f1(someFunc); // param被推导成函数指针
              // 类型是void(*)(int, double)
f2(someFunc); // param被推导成函数引用
              // 类型时void(&)(int, double)
```
