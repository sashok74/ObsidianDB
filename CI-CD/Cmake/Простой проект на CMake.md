Date: 2024-11-18 - Time: 11:55
___
Tags: #QUESTION #ANSWER
___
### Question:
### Простой проект на CMake
___
### Answer:
шаблон проект с библиотекой.
___
### Detail:
по шагам. 

  
#### Шаг 1: Простой вариант
   Структура папок:
```bash
otus
-si-sd (папка и название проекта)
--lib
---include
-----lib.h
-----version.h
---src
----lib.cpp
---CMakeLists.txt (для библиотеки) 
--app
---CMakeLists.txt  (для приложения)
---main.cpp
-CMakeLists.txt (общий)

```

содержание файлов:
```cpp

main.cpp
#include "lib.h"
#include <iostream>
int main (int, char **) {
    std::cout << "Version: " << version() << std::endl;
    std::cout << "Hello, world!" << std::endl;
    return 0;}

version.h:
#pragma once
#define PROJECT_VERSION_PATCH 3

lib.cpp
#include "lib.h"
#include "version.h"
int version() {return PROJECT_VERSION_PATCH;}

lib.h
#pragma once
int version();
```

### Описание файлов CMake

#### Главный `CMakeLists.txt`

В корневом `CMakeLists.txt` файле задаём минимальную версию CMake, название проекта, и добавляем поддиректории, содержащие приложение и библиотеку:

```cmake
cmake_minimum_required(VERSION 3.10)
project(HellowPack)

# Добавляем поддиректорию с библиотекой
add_subdirectory(lib)

# Добавляем поддиректорию с приложением
add_subdirectory(app)
```

#### `CMakeLists.txt` для библиотеки

В `lib/CMakeLists.txt` определите библиотеку, указав её исходные файлы и публичные заголовки:

```cmake
add_library(mylib SHARED src/mylib.cpp)

target_include_directories(mylib PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

set_target_properties(Versionlib PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS true)
```

#### `CMakeLists.txt` для приложения

В `app/CMakeLists.txt` создаём исполняемый файл и линкуем его с библиотекой:

```cmake
add_executable(myapp
    main.cpp
)

target_link_libraries(myapp
    PRIVATE mylib
)

target_include_directories(myapp PRIVATE
    ../lib/include
)
```

собираем:
```bash
mkdir build
cd ./build
cmake ..
cmake --build .
```

2. Сделаем выбор статическая или динамическая линковка
   В корневом `CMakeLists.txt` добавим опцию `BUILD_SHARED_LIBS`, которая отвечает за тип сборки библиотеки:

```cmake
cmake_minimum_required(VERSION 3.25)
project(MyProject)

# Опция для выбора типа сборки библиотеки: ON для динамической, OFF для статической
option(BUILD_SHARED_LIBS "Build using shared libraries" ON)

add_subdirectory(lib)
add_subdirectory(src)
```

Это позволит  передавать следующий аргумент при вызове CMake:
- `-DBUILD_SHARED_LIBS=ON` для сборки динамических библиотек
- `-DBUILD_SHARED_LIBS=OFF` для сборки статических библиотек

#### Шаг 2: Использование двух вариантов линковки

В файле CMakeList.txt, который находится в папке `lib`, учитываем опцию `BUILD_SHARED_LIBS`: 

```cmake
add_library(mylib src/lib.cpp)

target_include_directories(mylib PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

```

Теперь при добавлении библиотеки `add_library` автоматически выберется тип библиотеки на основании значения `BUILD_SHARED_LIBS`.

#### Шаг 3: Конфигурация и сборка с разным типом линковки

Для сборки проекта с конкретным типом библиотеки используем передачу опции через командную строку:

```bash
mkdir build && cd build
cmake .. -DBUILD_SHARED_LIBS=ON  # Для сборки как динамической библиотеки
# или
cmake .. -DBUILD_SHARED_LIBS=OFF # Для сборки как статической библиотеки
cmake --build .
```

#### Шаг 4: Добавляем CI-CD с помощью GitHub Action

Добавляем папку в корень репозитория .github/workflows
в нее добавляем первый файл yaml  с содержанием.
```yaml
name: Build ci-cd with dynamic and static linking

on: 
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        configuration: [Release, Debug]
        build_type: [SHARED, STATIC]
        
    steps:
      # Проверка репозитория
      - name: Checkout repository
        uses: actions/checkout@v2
      
      # Установка необходимых инструментов
      - name: Set up environment
        run: |
          sudo apt-get update
          sudo apt-get install -y build-essential cmake

      # Создание директории сборки
      - name: Create build directory
        run: mkdir -p ci-cd/build

      # Конфигурация и генерация CMake файлов
      - name: Configure CMake
        run: |
          cd ci-cd/build
          cmake .. -DCMAKE_BUILD_TYPE=${{ matrix.configuration }} -DBUILD_SHARED_LIBS=${{ matrix.build_type == 'SHARED' }}

      # Сборка проекта
      - name: Build project
        run: |
          cd ci-cd/build
          cmake --build .
```

#### Шаг 5: Добавляем генерацию файла version.h c помощью cmake.

добавляем файл шаблон в корень version.h.in
```cpp
#pragma once

#cmakedefine PROJECT_VERSION_PATCH @PROJECT_VERSION_PATCH@
```

меняем основной CMakeLists.txt
```CMake
set(PATCH_VERSION "1" CACHE INTERNAL "Patch version")
set(PROJECT_VESRION 0.0.${PATCH_VERSION})
project(HellowPack VERSION ${PROJECT_VESRION})

# Генерация файла version.h на основе шаблона version.h.in
# configure_file берет шаблон и заменяет значения в нем на реальные, полученные из переменных
configure_file(
    ${CMAKE_SOURCE_DIR}/version.h.in  # Путь к исходному файлу-шаблону
    ${CMAKE_BINARY_DIR}/version.h     # Куда вывести сгенерированный файл
)

target_include_directories(Versionlib PRIVATE "${CMAKE_BINARY_DIR}")
```

#### Шаг 6: Добавляем изменение версии с помощью GitHub action. 
передаем в cmake -DPATCH_VERSION=${{ github.run_number }}
файл jaml вносим следующие изменения.
```cmake
      # Конфигурация и генерация CMake файлов
      - name: Configure CMake
        run: |
          cd ci-cd/build
          cmake .. -DPATCH_VERSION=${{ github.run_number }} -DCMAKE_BUILD_TYPE=${{ matrix.configuration }} -DBUILD_SHARED_LIBS=${{ matrix.build_type == 'SHARED' }}
  

      # Сборка проекта

      # Печать версии
      - name: Print project version
        run: |
          cd ci-cd/build/app
          ./HelloApp
```

#### Шаг 7: Добавляем генерацию пакета deb. И формирование релиза.
Формируем deb только для версии release. в версиях shared и static
Возможно придется поставить права доступа в настройках Action разделе  WorkFlow permision. read and write permision
изменения в CMakeList.txt
```cmake
set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")
 

if(BUILD_SHARED_LIBS)
  set(CPACK_PACKAGE_FILE_NAME "HellowPack-${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}-shared")
else()
  set(CPACK_PACKAGE_FILE_NAME "HellowPack-${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}-static")
endif()

set(CPACK_PACKAGE_CONTACT rolliks@gmail.com)
include(CPack)
```

Создание пакета из консоли 
```bash
cmake ..
cmake --build .
cpack
```

изменения в GitHub Action
```yaml
# Условие для создания release:
      - name: Package with CPack
        if: matrix.configuration == 'Release'
        run: |
          cd ci-cd/build
          cpack -G DEB
          echo "Packages created for ${{ matrix.build_type }} ${{ matrix.configuration }}"
 

      # Генерация уникального тега
      - name: Generate unique release tag
        id: generate_tag
        run: |
          # Создаем уникальный тег на основе run_number и временной метки
          TAG_NAME="release-${{ github.run_number }}-$(date +%Y%m%d%H%M%S)"
          echo "Generated tag: $TAG_NAME"
          echo "tag_name=$TAG_NAME" >> $GITHUB_ENV  # Сохраняем его в переменной окружения
  

      # Создание релиза с уникальным тегом
      - name: Create Release
        if: matrix.configuration == 'Release'
        id: create_release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ env.tag_name }}    # Используем переменную окружения для тега
          release_name: "Release ${{ env.tag_name }}"  # Используем тот же тег как имя релиза
          draft: false
          prerelease: false
      - name: Upload Release Asset
        if: matrix.configuration == 'Release'
        uses: actions/upload-release-asset@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          upload_url: ${{ steps.create_release.outputs.upload_url }}
          asset_path: ./ci-cd/build/HellowPack-0.0.${{ github.run_number }}-${{ matrix.build_type == 'SHARED' && 'shared' || 'static' }}.deb
          asset_name: HellowPack-0.0.${{ github.run_number }}-${{ matrix.build_type == 'SHARED' && 'shared' || 'static' }}.deb
          asset_content_type: application/vnd.debian.binary-package
```

#### Шаг 8: Добавляем gtest с помощью conan 2.0

```bash
    └── tests/
        └── test_main.cpp
```
в корне создадим 
conanfile.txt
```txt
[requires]
gtest/1.15.0

[generators]
CMakeToolchain
CMakeDeps
```

изменения в CMakeLIsts.txt
в корне. 
для интеграции с cmake
добавили enable_testing() до add_subdirectory(tests) !!!
```cmake
enable_testing()
...
add_subdirectory(tests)

set_target_properties(HelloApp Versionlib tests PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
)

if (NOT EXISTS "${CMAKE_BINARY_DIR}/conan_toolchain.cmake")
   message(FATAL_ERROR "Conan toolchain file not found. Make sure to run `conan install` first.")
endif()
include(${CMAKE_BINARY_DIR}/conan_toolchain.cmake)
...
target_include_directories(tests PRIVATE "${CMAKE_BINARY_DIR}")
```

CMakeLists.txt в папке tests
```cmake
add_executable(tests test_main.cpp)
target_link_libraries(tests PRIVATE Versionlib)

target_include_directories(tests PRIVATE ../lib/include)
find_package(GTest CONFIG REQUIRED)
target_link_libraries(tests PRIVATE GTest::gtest GTest::gtest_main)

# Добавляем тестирование, интеграция с cmake
add_test(NAME VersionTest COMMAND tests)
```

из папки билда можно посмотреть и запустить все тесты:
```bast
ctest -N
ctest .
```

#### Шаг 8: Добавляем gtest в CI-CD
#### Шаг 9: Добавляем проект фильтрации ip адресов.
текущая структура проекта:
```bash
├── ci-cd
├── ip_filter
│   ├── app
│   │   ├── CMakeLists.txt
│   │   ├── include
│   │   │   └── ip_filters.h
│   │   ├── main.cpp
│   │   └── src
│   │       └── ip_filters.cpp
│   ├── build
│   ├── CMakeLists.txt
│   └── tests
│       ├── CMakeLists.txt
│       ├── ip_filter_result.tsv
│       ├── ip_filter.tsv
│       └── test_main.cpp
├── conanfile.txt
└── README.md
```
для копирования файлов в директорию компиляции тестов добавляем в 
CMakeListst.txt
```cmake
# Копируем файлы данных в папку со сборкой тестов
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/ip_filter.tsv
     DESTINATION ${TESTS_BUILD_DIR})
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/ip_filter_result.tsv
     DESTINATION ${TESTS_BUILD_DIR})
```

Добавляем GMock тесты. Используется GTest. Подключается так:
```cmake
# Находим Google Test и Google Mock

find_package(GTest CONFIG REQUIRED)

# Линкуем тесты с GTest и GMock
target_link_libraries(tests PRIVATE GTest::gtest GTest::gmock GTest::gtest_main GTest::gmock_main)
```

Для проверки работы используется расчет контрольной суммы выходного результата, на входе файл с тестовым списком.
``` bash
cat ip_filter.tsv | ./ip_filter | md5sum
```

#### Шаг 10:  Добавим для каждого задания разные файлы для GitHub Action.
отдельный файл yaml на каждое задание. добавлена секция paths:
```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'ip_filter/**'  
  pull_request:
    branches:
      - main
    paths:
      - 'ip_filter/**'
```

#### Шаг 11:  Добавим документирование кода с помощью Doxygen.


##### 11.1 Установите Doxygen:
   - **Локально**: установите Doxygen через пакетный менеджер (например, `sudo apt install doxygen` на Linux или скачайте с [официального сайта](https://www.doxygen.nl/)).
   - **На GitHub Actions**: Doxygen следует установить в процессе CI (будет описано далее).
##### 11.2 Установка Doxygen через пакетный менеджер или Conan?
   - Установка Doxygen через систему (e.g., `sudo apt install doxygen`) подходит, если он нужен только как инструмент для генерации документации. 
   - Установка через Conan:
     - Плюсы: воспроизводимость и независимость от настройки окружения.
     - Минусы: требует добавления Doxygen в зависимости каждого проекта.

   Решение: для локального использования можно установить Doxygen через пакетный менеджер, а для CI/CD установить через `apt` или как одноразовый шаг в workflow. Не обязательно добавлять в Conan.

##### 11.3 Настройка проекта:

###### a) Добавьте `Doxyfile`:
   - Сгенерируйте Doxygen конфигурацию в корне проекта:
     ```bash
     doxygen -g Doxyfile
     ```
   - В `Doxyfile` отредактируйте следующие параметры:
     ```
     PROJECT_NAME           = "Ваш проект"
     OUTPUT_DIRECTORY       = "docs"
     GENERATE_HTML          = YES
     GENERATE_LATEX         = NO
     INPUT                  = src include
     FILE_PATTERNS          = *.h *.hpp *.c *.cpp
     RECURSIVE              = YES
     GENERATE_TREEVIEW      = YES
     ```

##### 11.4  Добавление генерации в `CMakeLists.txt`
   - **Плюсы:**
     - Централизация: сборка проекта и документации контролируется из одного места.
     - Удобство: документация может генерироваться автоматически с другими целями (e.g., `make docs`).
   - **Минусы:**
     - Избыточность, если нужно генерировать документацию только в редких случаях.
     - Может усложнить CMake конфигурации.

   Решение: добавить генерацию документации как **опциональную CMake цель**, отключаемую по умолчанию:
   ```cmake
   find_package(Doxygen)

   if(DOXYGEN_FOUND)
       set(DOXYGEN_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/docs)
       configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile.in ${CMAKE_BINARY_DIR}/Doxyfile @ONLY)
       add_custom_target(docs
           COMMAND ${DOXYGEN_EXECUTABLE} ${CMAKE_BINARY_DIR}/Doxyfile
           WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
           COMMENT "Generating API documentation with Doxygen"
           VERBATIM)
   endif()
   ```

настройки в 

##### 11.4.1 Вариант настройки без шаблонного файла Doxyfile.in

в каждой директории свой файл Doxyfile с настройками. 
в Cmakelists добавляем

      ```cmake
     find_package(Doxygen REQUIRED)

     set(DOXYGEN_CONFIG_FILE ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile)
     add_custom_target(docs
         COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_CONFIG_FILE}
         WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
         COMMENT "Generating API documentation for sfinae"
         VERBATIM)
     ```

генерация.
```bash
cmake --build . --target docs
```

#### 11.5 Изменения в `GitHub Actions` YAML для генерации документации
   ```yaml
   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         # Проверка репозитория
         - name: Checkout repository
           uses: actions/checkout@v2
         
         # Установка необходимых инструментов
         - name: Set up environment
           run: |
             sudo apt-get update
             sudo apt-get install -y build-essential cmake python3-pip doxygen graphviz
             pip3 install conan>=2.0

         # Установка зависимостей через Conan
         - name: Install Conan and project dependencies
           run: |
             conan profile detect
             conan install . --output-folder=sfinae/build --build=missing -s build_type=Release

         # Конфигурация CMake с использованием Conan toolchain file
         - name: Configure and Build
           run: |
             cd sfinae/build
             cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
             cmake --build .

      # Генерация документации

      - name: Generate Documentation

        run: |
          cd sfinae
          doxygen Doxyfile

      # Загрузка документации как артефакта
      - name: Upload Documentation Artifact
        uses: actions/upload-artifact@v3
        with:
          name: documentation-sfinae
          path: sfinae/docs

      # Деплой документации на GitHub Pages
      - name: Deploy to GitHub Pages
        uses: JamesIves/github-pages-deploy-action@3.7.1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: gh-pages          # Укажите ветку для Pages (обычно gh-pages)
          folder: sfinae/docs/html       # Укажите папку, в которой сгенерирована документация
   ```

обратить внимание на folder часто в папке docs Doxyge генерирует еще папку html
```yaml
      # Деплой документации на GitHub Pages
      - name: Deploy to GitHub Pages
        uses: JamesIves/github-pages-deploy-action@3.7.1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: gh-pages          # Укажите ветку для Pages (обычно gh-pages)
          folder: sfinae/docs/html 
```

#### 11.6 Настройка страницы документации на github

Заходить на GitHub в настройки репозитория, далее пункт меня Pages
Build and deployment - Выставляем Deploy from branch
ветка  gh-pages/root

#### 11.7 готовим ветку для ревью

```bash
git checkout –b remove_all
```
Если нужно только часть файлов показать то я сперва удалили  всё ненужное
закомитил, а потом уже удалил нужное, которое восстановится в ветке ревью.
```bash
rm -rf allocators ci-cd ip_filter sfinae
git add -A
git commit -m 'удалил ненужное'
rm –rf *
git add .
git commit –m “remove all files for review”
git checkout –b review1
git log –-oneline
---  a6a905d (HEAD -> review1, remove_all) Remove all files for review.
--- Теперь можно revert-ить:
git revert a6a905d
git push -u origin review1
```
### Zero-Links

___
### Links

1.  ##### Doxygen
	1. [Using GitHub Actions to Publish Doxygen Docs to GitHub Pages](https://dev.to/denvercoder1/using-github-actions-to-publish-doxygen-docs-to-github-pages-177g)
	2. [GitHub Actions - Doxygen Documentation Deployment](https://ntamonsec.blogspot.com/2020/06/github-actions-doxygen-documentation.html)