# auto

## Item 6: Use the explicitly typed initializer idiom when auto deduces undesired types

用例子说话

```cpp
std::vector<bool> features(const Widget& w);

Widget w;
bool highPriority = features(w)[5];
```

没有任何问题，但如果使用auto

```cpp
auto highPriority = features(w)[5];
processWidget(w, highPriority); // 未定义行为
```

- 问题在于，使用auto的代码中，highPriority的类型已经不是bool了
- std::vector::operator[]对所有类型都返回引用，除了bool
- 返回的是`std::vector<bool>::reference`对象（是一个在`std::vector<bool>`中内嵌的class，代理类）
- 因为C++进制bits的引用，于是设计了这个对象

所以避免使用：

```cpp
auto someVar = expression of "invisible" proxy class type;
```
