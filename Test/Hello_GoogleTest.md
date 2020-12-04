# CMake + GoogleTest 单元测试入门

## 简介

GoogleTest 是一个跨平台的 C++ 测试框架，支持各种平台上不同类型的测试。被 Chromium, LLVM 和 OpenCV 等项目所使用。

If you write C++ code, googletest can help you!

## 环境

- OS: Ubuntu 20.10
- CMAKE: 3.16.3
- GoogleTest: 1.10.0

## 构建 GoogleTest

```bash
git clone https://github.com/google/googletest.git -b release-1.10.0
cd googletest
mkdir build
cd build
cmake ..
make
sudo make install
```

## Demo

我们来体验一下

bubblesort.h

```cpp
#include <vector>

void swap(std::vector<int> &data, int i, int j);
void bubblesort(std::vector<int> &data, int n);
```

bubblesort.cpp

```cpp
#include "bubblesort.h"

void swap(std::vector<int> &data, int i, int j)
{
    int temp = data[i];
    data[i] = data[j];
    data[j] = temp;
}
void bubblesort(std::vector<int> &data, int n)
{
    for(int i = 0; i < n - 1; ++i)
    {
        for(int j = 0; j < n - i - 1; ++j)
        {
            if(data[j] > data[j + 1])
                swap(data, j, j + 1);
        }
    }
}
```

main.cpp

```cpp
#include <iostream>
#include <vector>
#include "bubblesort.h"

int main(int argc, char *argv[])
{
    std::vector<int> a = {7, 1, 3, 4, 2, 8, 5, 6, 0};
    bubblesort(a, a.size());
    for(int i = 0; i < a.size(); ++i)
    {
        std::cout << a[i] << " ";
    }
    std::cout << std::endl;
    return 0;
}
```

test.cpp

```cpp
#include "bubblesort.h"
#include <gtest/gtest.h>

TEST(test, case1)
{
    std::vector<int> a = {7, 1, 3, 4, 2, 8, 5, 6, 0};
    std::vector<int> b = {0, 1, 2, 3, 4, 5, 6, 7, 8};
    bubblesort(a, a.size());
    EXPECT_EQ(a, b);
}

int main(int argc, char *argv[])
{
    ::testing::InitGoogleTest(&argc, argv);
    
    return RUN_ALL_TESTS();
}
```

CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 2.8)

project(bubble-sort)

find_package(GTest REQUIRED)

include_directories(${GTEST_INCLUDE_DIRS})

enable_testing()

add_executable(sort bubblesort.cpp main.cpp)
add_executable(unit_test test.cpp bubblesort.cpp)

target_link_libraries( unit_test
        ${GTEST_BOTH_LIBRARIES}
        pthread )

add_test(test unit_test)
```

## 运行

```bash
cmake ..
make
```

`make test`结果：

```
Running tests...
Test project /home/chronoby/Development/GoogleTest/hello/build
    Start 1: test
1/1 Test #1: test .............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec
```

`./unit_test` 结果：

```
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from test
[ RUN      ] test.case1
[       OK ] test.case1 (0 ms)
[----------] 1 test from test (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
```

Hello, GoogleTest!