# Accustoming yourself to C++

## Item 1: view C++ as a federation of languages

## Item 2: prefer consts, enums and inline to #defines

- 当你`#define ASPECT_RATIO 1.653`，记号名称ASPECT_RATIO可能没有进入记号表内。当你运用此常量获得一个编译错误信息，也许会提到1.653而不是ASPECT_RATIO，会引起你或者使用你代码的人的困惑

### 常量

解决办法：`const double AspectRatio = 1.653;`

- 常量定义通常被放在头文件里，有必要将指针声明为const

### class专属常量

- 声明为静态成员变量

```cpp
class GamePlayer {
private:
    static const int NumTurns = 5;
    int scores[NumTurns];
}
```

- 此为NumTurns的声明式而非定义式。通常C++要求对你使用的任何东西提供定义式，但如果他是个class专属常量优势static且为整数类型(int, char, bool)，则需特殊处理
- 只要不取它们的地址，或纵使你不去地址而编译器坚持要一个定义式，不虚提供定义式如下：

```cpp
const int GamePlayer::NumTurns; //定义式
```

- 将这个式子放进实现文件而非头文件
- 由于class常量在声明时已经获得初值，此定义可以不再设初值

### enum hack

- 万一编译器（错误地）不允许static整数型class常量完成in class初值设定，可改用所谓的"the enum hack"做法
- 理论基础是：一个属于枚举类型的数值可以权充int被使用

```cpp
class GamePlayer {
private:
    enum { NumTurns = 5 };
    int scores[NumTurns];
}
```
