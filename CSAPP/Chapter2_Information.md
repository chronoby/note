# Chapter2 Information

## 信息存储

### C语言移位

- C语言标准没有明确定义对于有符号数应该使用哪种类型的右移——算数右移和逻辑右移都可以
- 这意味着可能会发生可移植性问题
- 几乎所有编译器/机器都会使用算数右移

## 整数表示

### 补码

- 补码的范围是不对称的：|T_min| = |T_max| + 1
- 因为一般的位模式表示负数，而另一半表示非负数，0的存在使得能表示的正数比负数少一个
- 最大的无符号数数值刚好比补码最大值的两倍大一点：U_max = 2T_max + 1
- 反码和原码对于数字0有两种不同的编码方式
- 补码(Two's complement)和反码(Ones' complement)撇号的位置不同

### 有符号数和无符号数之间的转换

- C语言支持有符号数和无符号数的强制类型转换
- size_t被定义为unsigned int

## 整数运算

- 有符号数和无符号数的加法规则和溢出检测
(reserved)

## 浮点数

IEEE浮点标准用 V = (-1) ^ s * M * 2 ^ E 的形式来表示一个数

- 符号(sign) s 决定正数(s = 0)和负数(s = 1)
- 尾数(significand) M是一个二进制小数，范围是1 ～ 2-ε，或者是 0 ~ 1-ε
- 阶码(exponent) E的作用是对浮点数加权，这个权重是2的E次幂