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
2. 利用__type_traits<T>判断该类的析构函数是否为trivial。若是(__true_type)，则什么都不做；若否(__false_type)，以循环方式遍历整个范围，且每经历一个对象调用一次destroy()

(```::operator new``` is explained in ./cpp_tips.md)

### 空间的分配和释放，std::alloc

SGI的设计方法：
- 向system heap请求空间
- 考虑多线程状态
- 考虑内存不足时的应变措施
- 考虑过多小型区块可能造成的内存碎片问题

