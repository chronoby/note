# 迭代器(Iterators)概念与traits编程技法

Iterator模式：提供一种方法，使之能够依序巡防某个容器所含的各个元素，而又无需暴露改容器的内部表述方式

## 迭代器设计思维

STL的核心思想：将容器和算法分开，彼此独立设计，最后将他们融合在一起

## Iterator是一种smart pointer

- 迭代器是一种行为类似指针的对象，常见的操作是dereference和member access
- 迭代器最重要的工作时对operator*和operator->进行重载

## Iterator associated types

- 假设算法中有必要声明一个变量，以“迭代器所指对象的类型”为类型
- 解决办法：利用function template中的参数推导(argument deducation)机制，例如：

```cpp
template <class I, class T>
void func_impl(I iter, T t)
{
    T temp; // 这里解决了问题，T即为迭代器所指类型，本例中为int
    // ... 原本func()应该做的工作
};
template <class I>
inline void func(I iter)
{
    func_impl(iter, *iter)
}
int main()
{
    int i;
    func(&i);
}
```

我们以func()为对外接口，将实际操作放在func_impl()中，编译器进行了template的参数推导

## Traits 编程技法

- 如果value type必须用于函数的返回值，上述参数推导机制无法实现
- 声明内嵌类型。隐晦的陷阱：并不是所有迭代器都是class（如原生指针）。如果不是class type，无法定义内嵌类别

### Template partial specialization

- 如果class template拥有一个以上template参数，我们可以针对其中某个（或数个，但非全部）template参数进行特化，即我们可以在泛化设计中提供一个特化版本
