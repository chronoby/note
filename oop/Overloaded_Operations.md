# 重载

## 基本概念

### 重载运算符的返回类型通常情况下应该与其内置版本的返回类型兼容：
- 逻辑运算符和关系运算符应该返回bool
- 算术运算符应该返回一个类类型的值
- 赋值运算符和符合赋值运算符应该返回左侧运算对象的一个引用

### 选择作为成员或者非成员
- 赋值（=）、下标（[ ]）、调用（()）和成员访问箭头（->）运算符必须是成员
- 复合赋值运算符一般来说必须是成员
- 改变对象状态的运算符或者与给定类型密切相关的运算符，如递增、递减和解引用运算符，通常应该是成员
- 具有对称性的运算符可能转换任意一端的运算对象，如算数，相等性，关系和位置运算符等，因此它们通常应该是普通的非成员函数

## 重载输出运算符 <<

- 第一个形参是非常量ostream对象的引用。ostream是非常量是因为向流写入内容会改变其状态，而该形参是引用是因为我们无法复制一个ostream对象
- 第二个形参是一个常量的引用，是引用的原因是我们希望避免复制形参，是常量的原因是打印对象不会改变对象的内容。
- 为了与其他输出运算符保持一致，返回ostream形参
- 输出运算符应尽量减少格式化操作

e.g.
```cpp
ostream &operator<<(ostream &os, const Sales_data &item)
{
    os << item.isbn() << " " << item.units_sold << " " << item.revenue << " " << item.avg_price(); 
    return os;
}
```
## 重载输入运算符 >>

e.g.

```cpp
istream &operation >>(istream &is, Sale_data &item)
{
    double price;
    is >> item.bookNo >> item.units_sold >> price;
    if(is)
        item.revenue = item.units_sold * price;
    else
        item = Sales_data();
    return is;
}
```

## 算术和运算符

我们吧算术和关系运算符定义为非成员函数以允许对左侧或右侧的运算对象进行转换。因为这些运算符一般不需要改变运算对象的状态，所以形参都是对常量的引用。

e.g.

```cpp
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs; // copy data members from lhs into sum
    sum += rhs;           // add rhs into sum
    return sum;
}
```

## 相等运算符

e.g.
```cpp
bool operator==(const Sales_data &lhs, const Sales_data &rhs)
{
    return lhs.isbn() == rhs.isbn() && lhs.units_sold == rhs.units_sold && lhs.revenue == rhs.revenue;
}
bool operator!=(const Sales_data &lhs, const Sales_data &rhs)
{
    return !(lhs == rhs);
}
```

## 关系运算符

## 赋值运算符
e.g.

```cpp
StrVec &StrVec::operator=(initializer_list<string> il)
{
    // alloc_n_copy allocates space and copies elements from the given range
    auto data = alloc_n_copy(il.begin(), il.end());
    free(); // destroy the elements in this object and free the space
    elements = data.first; // update data members to point to the new space
    first_free = cap = data.second; 
    return *this;
}
```
## 复合赋值运算符

## 下标运算符

- 下标运算符必须是成员函数
- 如果一个类包含下标运算符，则它通常会定义两个版本：一个返回普通饮用，另一个是类的常量成员并且返回常量引用

e.g.
```cpp
std::string& operator[](std::size_t n) { return elements[n]; } 
const std::string& operator[](std::size_t n) const { return elements[n]; }
```

## 递增和递减运算符
由于其改变的是所操作对象的状态，所以建议将其设定为成员函数

e.g.
```cpp
// prefix: return a reference to the incremented/decremented object
StrBlobPtr& StrBlobPtr::operator++()
{
    // if curr already points past the end of the container, can't increment it
    check(curr, "increment past end of StrBlobPtr");
    ++curr; // advance the current state
    return *this;
}
StrBlobPtr& StrBlobPtr::operator--()
{
    // if curr is zero, decrementing it will yield an invalid subscript
    --curr; // move the current state back one element
    check(-1, "decrement past begin of StrBlobPtr"); return *this;
}
```

要区分前置和后置运算符，后置版本接受一个额外的（不被使用）int类型的形参。
e.g.

```cpp
// postfix: increment/decrement the object but return the unchanged value
StrBlobPtr StrBlobPtr::operator++(int)
{
    // no check needed here; the call to prefix increment will do the check
    StrBlobPtr ret = *this; // save the current value
    ++*this; // advance one element; prefix ++ checks the increment
    return ret; // return the saved state
}
StrBlobPtr StrBlobPtr::operator--(int)
{
    // no check needed here; the call to prefix decrement will do the check
    StrBlobPtr ret = *this; // save the current value
    --*this; // move backward one element; prefix -- checks the decrement
    return ret; // return the saved state
}
```

- 显式调用后置运算符：要通过函数调用的方式调用后置版本，则必须为它的整形参数传递一个值
```cpp
p.operator++(0); // 后置
p.operator++();  // 前置
```

## 成员访问运算符
- 在迭代器类及智能指针类中常常用到解引用运算符和箭头运算符
- 将这两个运算符定义成const成员，因为获取一个元素不会改变StrBlobPtr对象的状态
- 返回值分别是非常量string的引用或指针，因为一个StrBlobPtr只能绑定到非常量的StrBlob对象
```cpp
class StrBlobPtr {
public:
    std::string& operator*() const 
    { 
        auto p = check(curr, "dereference past end");
        return (*p)[curr]; // (*p) is the vector to which this object points
    } 
    std::string* operator->() const
    { // delegate the real work to the dereference operator
        return & this->operator*(); 
    }
    // other members as before
};
```

## 函数调用运算符
- 像函数一样使用类的对象，且比类更加灵活

```cpp
struct absInt 
{ 
    int operator()(int val) const { return val < 0 ? -val : val; }
};
```

### lambda 表达式
```cpp
void abssort(float* x, unsigned n) 
{
    std::sort(x, x + n,
        // Lambda expression begins
        [](float a, float b) 
        {
            return (std::abs(a) < std::abs(b));
        } // end of lambda expression
    );
}
```
类似于

```cpp
class ShorterString
{
public:
    bool operator()(const string &s1, const string &s2) const
    {
        return s1.size() < s2.size();
    }
}
```

调用：
```cpp
sort(words.begin(), words.end(), ShorterString());
```

## 重载、类型转换和运算符
reserved
