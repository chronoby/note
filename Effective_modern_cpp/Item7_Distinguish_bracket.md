# Moving to modern C++

## Item 7: Distinguish between () and {} when creating objects

- 使用花括号初始化一个vector

```cpp
std::vector<int> v{1, 2, 3};
```

- 为类非静态数据成员指定默认初始值

```cpp
class Widget
{
private:
    int x{0};       //没问题，x初始值为0
    int y = 0;      //同上
    int z(0);       //错误！
}
```

- 不可拷贝的对象使用花括号或小括号初始化，但不能使用=初始化

```cpp
std::vector<int> x{0};  // correct
std::atomic<int> y(0);  // correct
std::atomic<int> z = 0; // error!
```

- 花括号表达式有一个特性：不允许内置类型隐式的变窄转换(narrowing conversion)

```cpp
double x, y, z;
int sum1{x + y + z}; // 错误！三个double不能
```

- 使用小括号和=则可以，因为由于历史遗留问题它们必须兼容老旧代码

```cpp
int sum2(x + y +z);         //可以（表达式的值被截为int）
int sum3 = x + y + z;       //同上
```

- 另一个值得注意的特性是括号表达式对于C++最令人头疼的解析问题
- C++规定任何能被决议为一个声明的东西必须被决议为声明。这个规则的副作用是：当他们想创建一个使用默认构造函数构造的对象，却不小心变成了函数声明。问题的根源是如果你想使用一个实参调用一个构造函数，你可以这样做：

```cpp
Widget w1(10);              //使用实参10调用Widget的一个构造函数
```

但是如果你尝试使用一个没有参数的构造函数构造对象，它就会变成函数声明：

```cpp
Widget w2();                //最令人头疼的解析！声明一个函数w2，返回Widget
```

由于函数声明中形参列表不能使用花括号，所以使用花括号初始化表明你想调用默认构造函数构造对象就没有问题：

```cpp
Widget w3{};                  //调用没有参数的构造函数构造对象
```

### 括号初始化的缺点

- 括号初始化和std::initializer_list和构造函数重载决议本来就不清不楚的暧昧关系进一步混乱
- 你越喜欢用auto，越不能用括号初始化

---

- 在构造函数调用中，只要不包含std::initializer_list参数，花括号和小括号初始化会产生一样的结果
- 然而，如果一个或者多个构造函数的参数是std::initializer_list，使用括号初始化语法绝对比传递一个std::initializer_list实参要好。
- 只要某个调用能使用括号表达式，编译器就会使用它

```cpp
class Widget
{
public:
    Widget(int i, bool b);
    Widget(std::initializer_list<long double> il);
};

Widget w1(10, true);     // 使用小括号初始化
                         //调用第一个构造函数

Widget w2{10, true};     // 使用花括号初始化
                         // 调用第二个构造函数
                         // (10 和 true 转化为long double)

Widget w3(10, 5.0);      // 使用小括号初始化
                         // 调用第二个构造函数

Widget w4{10, 5.0};      // 使用花括号初始化
                         // 调用第二个构造函数
                         // (10 和 5.0 转化为long double)
```

- 甚至普通的构造函数和移动构造函数都会被std::initializer_list劫持

```cpp
class Widget
{
public:  
    Widget(int i, bool b);
    Widget(std::initializer_list<long double> il);
    operator float() const;
};

Widget w5(w4);               // 使用小括号，调用拷贝构造函数

Widget w6{w4};               // 使用花括号，调用std::initializer_list构造函数

Widget w7(std::move(w4));    // 使用小括号，调用移动构造函数

Widget w8{std::move(w4)};    // 使用花括号，调用std::initializer_list构造函数  
```

- 编译器热衷于把括号初始化与std::initializer_list构造函数匹配，热衷程度甚至超过了最佳匹配

```cpp
class Widget
{
public:  
    Widget(int i, bool b);
    Widget(int i, double d);
    Widget(std::initializer_list<bool> il);
    ...
};
Widget w{10, 5.0};      //错误！要求变窄转换
```

- 这里，编译器会直接忽略前面两个构造函数，尝试调用第三个构造函数
- 只有当没办法吧括号初始化中的实参转化为std::initializer_list时，编译器才会回到正常的函数决议流程中
- 假如你使用的花括号初始化是空集，并且你欲构建的对象有默认构造函数，也有std::initializer_list构造函数，空的花括号意味着没有实参，不是一个空的std::initializer_list
- 如果你想调用std::initializer_list构造，你就得创建一个空花括号的实参来表明你想调用一个std::initializer_list构造函数，它的实参是一个空值。

```cpp
Widget w4({});        // 调用std::initializer_list
Widget w5{{}};        // 同上
```

在vector中

```cpp
std::vector<int> v1(10, 20);    //使用非std::initializer_list
                                //构造函数创建一个包含10个元素的std::vector
                                //所有的元素的值都是20
std::vector<int> v2{10, 20};    //使用std::initializer_list
                                //构造函数创建包含两个元素的std::vector
                                //元素的值为10和20
```

两个结论：

- 作为一个类库作者，你需要意识到如果你的一堆构造函数中重载过一个或者多个std::initializer_list， 用户代码如果使用了括号初始化，可能只会看到你重载的std::initializer_list这一个版本的构造函数。 因此，你最好把你的构造函数设计为不管用户是小括号还是使用花括号进行初始化都不会有什么影响。
- 作为一个类库使用者，你必须认真的在花括号和小括号之间选择一个来创建对象。

Things to Remember:

- 括号初始化是最广泛使用的初始化语法，它防止变窄转换，并且对于C++最令人头疼的解析有天生的免疫性
- 在构造函数重载决议中，括号初始化尽最大可能与std::initializer_list参数匹配，即便其他构造函数看起来是更好的选择
- 对于数值类型的std::vector来说使用花括号初始化和小括号初始化会造成巨大的不同
- 在模板类选择使用小括号初始化或使用花括号初始化创建对象是一个挑战。
