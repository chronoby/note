# Allocator

## SGI空间配置器

### C++内存配置和释放操作

```cpp
class Foo {...};
Foo * pf = new Foo;
delete pf;
```

- new内含两阶段操作

1. 调用```::operator new```配置内存
2. 调用```Foo::Foo()```构造对象
(```::operator new``` is explained in ./cpp_tips.md)

- delete也包含两阶段操作

1. 调用```Foo::~Foo()```将对象析构
2. 调用```::operator delete```释放内存

- STL_allocator决定将这两阶段操作区分开来。

1. 内存配置操作由alloc::allocate()负责，内存释放操作由alloc::deallocate()负责
2. 对象构造由::construct()负责，对象析构由::destroy()负责

### 构造和析构基本工具：construct()和destroy()

- construct()接受一个指针p和一个初值value，该函数将初值设定到指针所指空间上，C++的replacement new可用来完成这一任务
- destroy()有两个版本

1. 接受一个指针。直接调用该对象的析构函数即可
2. 接受first和last两个迭代器，将[first, last)范围内的对象析构掉

为了防止范围很大且每个对象的析构函数都是trivial destructor，进行以下操作：

1. 利用value_type()获得迭代器所指对象的类型
2. 利用`__type_traits<T>`判断该类的析构函数是否为trivial。若是
(__true_type)，则什么都不做；若否(__false_type)，以循环方式遍历整个范围，且每经历一个对象调用一次destroy()

(```::operator new``` is explained in ./cpp_tips.md)

### 空间的分配和释放，std::alloc

SGI的设计方法：

- 向system heap请求空间
- 考虑多线程状态
- 考虑内存不足时的应变措施
- 考虑过多小型区块可能造成的内存碎片问题

源代码层面：

- 以`::operator new()`和`::operator delete()`完成内存的分配和释放

针对小型区块可能造成的内存破碎问题，采用双层配置器。

- 第一级配置器使用`malloc()`和`free()`
- 第二级配置器视情况采用不同的策略：
  - 当配置区块超过128byte时，视为“足够大”，调用第一级配置器
  - 当配置区块小于128byte时，视为“过小”，为了降低额外负担(overhead)，使用复杂的memory pool整理方式

### 第一级配置器 __malloc_alloc_template

- 一级配置器以malloc(), free(), realloc()等C函数执行实际的内存分配、释放、重分配操作，并实现出类似C++ new-handler的机制
- 所谓C++ new-handler机制是，它可以要求系统在内存分配需求无法满足时，调用一个你所指定的函数，即一旦::operator new无法完成任务，在丢出std::bad_alloc异常状态之前，会先调用由客户端指定的处理例程，该处理例程被称为new-handler
(C++ new-handler见./cpp_tips.md)

### 第二级配置器 __default_alloc_template

- 第二级配置避免太多小额区块造成的内存碎片
- 小额区块带来的不仅是内存碎片，配置时的额外负担也是一个问题
- 当区块小于128 byte时，以内存池管理：维护一系列大小为8 byte倍数的free-list

### 空间配置函数allocate()

- __default_alloc_template的标准接口函数
- 先判断区块大小
  - 大于128 bytes就调用第一级配置器
  - 小于128 bytes就检查对应free list，若有可用区块，直接拿来用；诺没有，则将区块大小上调至8倍数边界，然后调用refill()，准备为free list重新填充空间

### 空间释放函数deallocate()\

- 判断区块大小
  - 大于128 bytes调用第一级配置器
  - 小于128 bytes找出对应free list，将区块回收

### 重新填充free lists

- 从内存池取空间为free lsits重新填充空间

### 内存池

分为多种情况

- 内存充足：则调出20个区块返回给free list
- 内存不足以提供20个区块，但还足以供应一个以上的区块，拨出不足20个区块的空间出去，nobjs参数修改为实际能提供的区块数
- 一个区块都无法供应，则使用malloc()中分配内存
- 若system heap的空间不足，则利用out-of-memory处理机制（类似new-handler机制），释放其他内存来使用
- 否则抛出bad_alloc异常

## 内存基本处理工具

reversed
