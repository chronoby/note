# 面向对象程序设计

### 派生类的声明：
```cpp
class bulk_quote;               // 正确
class bulk_quote : public quote;// 错误
```

- 如果我们想将某个类用作基类，则改类必须已经定义而非仅仅声明
- 必须为每个虚函数（纯虚函数除外）提供定义，因为编译器无法确定运行时会使用哪个函数

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

### 纯虚函数
```cpp
double net_price(std::size_t) const = 0;
```

- 我们也可以为纯虚函数提供定义，不过函数体必须定义在类的外部
- 含有纯虚函数的类是抽象基类
- 派生类构造函数只初始化它的直接基类

### 访问控制和继承

- 派生类的成员或友元只能通过派生类对象来访问基类的受保护成员。派生类对于一个基类对象中的受保护成员没有任何访问特权
```cpp
class Base 
{
protected:
    int prot_mem; // protected member
};
class Sneaky : public Base 
{
    friend void clobber(Sneaky&); // can access Sneaky::prot_mem
    friend void clobber(Base&); // can't access Base::prot_mem
    int j; // j is private by default
};
// ok: clobber can access the private and protected members in Sneaky objects
void clobber(Sneaky &s) { s.j = s.prot_mem = 0; }
// error: clobber can't access the protected members in Base
void clobber(Base &b) { b.prot_mem = 0; }
```

- 虚函数表（待学习）
e.g.
```cpp
class A {
public:
    A() {
        f();
    }
    virtual f() {}
}
class B {
public:
    B() {}
    void f() {}
}
int main() {
    A* p = new B(); // 调用的是A中的f()
}
```

### 共有、私有和受保护继承

- 派生访问说明符对于派生类的成员及友元能否访问其基类的成员没有影响
- 派生访问说明符的目的是控制派生类用户对于基类成员的访问权限

### 派生类向基类转换的可访问性
- 只有当D共有地继承B时，用户代码才能使用派生类向基类的转换；如果D继承B的方式是受保护的或者私有的，则用户代码不能使用改转换
- 无论D以什么方式继承B，D的成员函数和友元都能使用派生类向基类的转换
- 如果D继承B的方式是共有的或者受保护的，则D的派生类的成员和友元可以使用D向B的类型转换；反之，如果D继承B的方式是私有的，则不能使用

### 改变个别成员的可访问性
- using声明

```cpp
class Base {
public:
    std::size_t size() const { return n; }
protected:
    std::size_t n;
};
class Derived : private Base { // note: private inheritance
public:
    // maintain access levels for members related to the size of the object
    using Base::size;
protected:
    using Base::n;
};
```
- 用using声明语句改变了成员的可访问性。
- Derived的用户可以试用size成员，Derived的派生类能使用n

### 默认的继承保护级别
```cpp
class Base { /* ... */ };
struct D1 : Base { /* ... */ }; // public inheritance by default
class D2 : Base { /* ... */ }; // private inheritance by default
```

- struct和class的区别仅在于默认成员访问说明符和默认派生访问说明符

---

### 继承类中的类作用域

