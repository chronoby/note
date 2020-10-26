# 关于对象(Object lessons)

## 加上封装后的布局成本

- 加上封装后，没有增加布局成本
- member function虽然含在class的声明之内，却不出现在object中，每一个non-inline member function只会诞生一个函数实体
- 每一个拥有零个或一个定义的inline function则会在每一个使用者身上产生一个函数实体
- C++在布局以及存取时间上主要的额外负担是有virtual引起的，包括
  - virtual function机制
  - virtual base class
