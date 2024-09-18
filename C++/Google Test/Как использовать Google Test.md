Date: 2024-09-01 - Time: 14:18
___
Tags: #QUESTION #ANSWER
___
### Question:
### Как установить и использовать Google Test
___
### Answer:
1. Установка с репозитория
2. Компиляция под нужную систему
3. Настройка проекта CMake 
4. Написание теста.
___
### Detail:
1 .
```bash
git clone https://github.com/google/googletest.git
cd googletest
mkdir build
cd build
cmake --build .. -DCMAKE_INSTALL_PREFIX=c:/libraries/gtest-dll -DBUILD_SHARED_LIBS=ON
cmake --build . --target install
```
для статических библиотек можно собрать  -DBUILD_SHARED_LIBS=ON но у меня не заработало.
в основной cmake делаем ссылку на папку с тестами и добавляем следующее
```cmake

enable_testing()
add_subdirectory(tests)
```

в папке с тестами делаем свой CmakeList.txt
```cmakelist
cmake_minimum_required(VERSION 3.10)

project(ParamListTests)

##set(GTEST_ROOT "C:/Libraries/gtest")

set(CMAKE_PREFIX_PATH "C:/Libraries/gtest-dll")

  

# Установите стандарт C++.

set(CMAKE_CXX_STANDARD 17)

  

# Подключение GoogleTest.

enable_testing()

find_package(GTest REQUIRED)

include_directories(${GTEST_INCLUDE_DIRS})

# Вывести сообщение об успешном поиске GoogleTest.

message(STATUS "GTest found: ${GTEST_INCLUDE_DIRS}")

message(STATUS "GTest libraries: ${GTEST_LIBRARIES}")

  

find_package(Threads REQUIRED)

  

# Добавьте файлы теста.

add_executable(paramlist_test paramlist_test.cpp test_ParamList.cpp)

target_link_libraries(paramlist_test ${GTEST_LIBRARIES} ) ## убрал Threads::Threads

  

# Включите тестирование с помощью CTest.

add_test(NAME paramlist_test COMMAND paramlist_test)

## C:\VStudioProjects\optAny\tests\test_ParamList.cpp
```

странно GTEST_ROOT у меня не сработал и находил другой gtest а с ним не работало... на это надо смотреть.
 далее пишем тесты можно в разных файлах но в одном файле должно быть 
 ```cpp
 // Основная функция, запускающая все тесты

int main(int argc, char **argv) {

    ::testing::InitGoogleTest(&argc, argv);

    return RUN_ALL_TESTS();

}
```
В остальных тесты по правилам. минимум такой:
```cpp
#include "gtest/gtest.h"

  

TEST(CMParamListTest, SetAndGetParam) {

    CMParamList params;

    params.set_param("param_name_1", std::string("value1"));

    params.set_param("param_name_2", 123.45);

  

    EXPECT_EQ(params.get_param<std::string>("param_name_1"), "value1");

    EXPECT_EQ(params.get_param<double>("param_name_2"), 123.45);

}
```
___
### Zero-Links
[00_TESTS](../../__Z_CORE/00_TESTS.md)
___
### Links
