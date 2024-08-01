Date: 2024-05-27 - Time: 18:59
___
Tags: #QUESTION #ANSWER
___
### Question:
### Как cmake выбирать debug или release?
___
### Answer:
```bash
cmake --build build --config Release
```
___
### Detail:
**How do I run CMake for each target type (debug/release)?**

Во-первых, Debug/Release называются конфигурациями в cmake (nitpick).

Если вы используете один генератор конфигурации (Ninja/Unix-Makefiles), вы должны указать [CMAKE_BUILD_TYPE](https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html?highlight=cmake_build_type).

Like this:

```bash
# Configure the build
cmake -S . -B build/ -D CMAKE_BUILD_TYPE=Debug

# Actually build the binaries
cmake --build build/

# Configure a release build
cmake -S . -B build/ -D CMAKE_BUILD_TYPE=Release

# Build release binaries
cmake --build build/
```

Для генераторов с несколькими конфигурациями все немного по-другому (Ninja Multi-Config, Visual Studio).

```shell
# Configure the build
cmake -S . -B build

# Build debug binaries
cmake --build build --config Debug

# Build release binaries
cmake --build build --config Release
```

Если вам интересно, зачем это нужно, то это потому, что cmake не является системой сборки. Это система мета-сборки (т.е. система сборки, которая объединяет системы сборки). В основном это результат работы с системами сборки, которые поддерживают несколько конфигураций в одной сборке. Если вы хотите получить более глубокое представление, я бы посоветовал немного почитать о cmake в книге Крейга Скотта "Профессиональный CMake: практическое руководство
___
### Zero-Links
[00_CPP](__Z_CORE/00_CPP.md)
___
### Links
