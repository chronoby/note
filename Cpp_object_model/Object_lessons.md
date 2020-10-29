# 关于对象(Object lessons)

## 加上封装后的布局成本

- 加上封装后，没有增加布局成本
- member function虽然含在class的声明之内，却不出现在object中，每一个non-inline member function只会诞生一个函数实体
- 每一个拥有零个或一个定义的inline function则会在每一个使用者身上产生一个函数实体
- C++在布局以及存取时间上主要的额外负担是有virtual引起的，包括
  - virtual function机制
  - virtual base class

## C++ 对象模型

- non-static data member被配置于每一个class object之内
- static data member被存放在所有的class object之内
- static和non-static function member也被存放在class object之外
- virtual function则用两个步骤支持
  - 每一个class产生出一堆指向virtual function的指针，放在表格之中，这个表格被称为virtual table(vtbl)
  - 每一个class object被添加了一个指针，指向相关的virtual table(vptr)，vptr的设定和重置都由每一个class的constructor, destructor和copy assignment运算符自动完成。每一个class所关联的type_info object(用以支持runtime type identitication)也经由virtual table被指出来，通常放在表格的第一个slot处
- 这个模型的主要优点在于其空间和存取时间的效率
- 主要缺点为：如果应用程序代码本身未曾改变，但用到的class object的nonstatic data member有所修改，那么那些应用程序代码同样得重新编译

## 关键词带来的差异
