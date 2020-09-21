# 面向对象程序设计

- 派生类的声明：
```cpp
class bulk_quote;               // 正确
class bulk_quote : public quote;// 错误
```

- 如果我们想将某个类用作基类，则改类必须已经定义而非仅仅声明

- 在类名后跟一个关键词final，阻止此类被继承
```cpp
class NoDerived final {...}; // NoDerived不能作为基类
```

- 使用override关键字来说明派生类中的虚函数
```cpp
struct B 
{
    virtual void f1(int) const;
    virtual void f2();
    void f3();
};
struct D1 : B
{
    void f1(int) const override; // ok: f1 matches f1 in the base
    void f2(int) override;       // error: B has no f2(int) function
    void f3() override;          // error: f3 not virtual
    void f4() override;          // error: B doesn't have a function named f4
};
```

- 如果某次函数调用使用了默认实参，则该实参值由本次调用的静态类型决定

- 为了强迫执行虚函数的某个特定版本，使用作用域运算符：
```cpp
double undiscounted = baseP->Quote::net_price(42);
```
