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

### ParamType 是个通用的引用（Universal Reference）

### ParamType 既不是指针也不是引用
