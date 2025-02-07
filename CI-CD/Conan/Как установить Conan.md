Date: 2024-11-12 - Time: 17:07
___
Tags: #CONAN
___
# Conan
___ 
### Shot Descripion: Установка и использование пакетного менеджера  Conan.

___
### Main info:
https://docs.conan.io
___
### Zero-Links
[00_CPP](../../__Z_CORE/00_CPP.md)
___
### Links
https://docs.conan.io
[Генераторы Canon](Генераторы%20Canon.md)
На debian установился так
```bash
$ apt-get install pipx
$ pipx ensurepath
```

2. Restart your terminal and then install Conan using `pipx`:
```bash
$ pipx install conan
```

в проекте cmake сперва инициализируем.
```bash
conan profile detect --force
```

в CMakeLists.txt 
минимум который нужен для подключение пакета
```cmake
find_package(ZLIB REQUIRED)

add_executable(${PROJECT_NAME} src/main.c)
target_link_libraries(${PROJECT_NAME} ZLIB::ZLIB)
	```

для Conan создаем файл со списком библиотек которые хотим установить.
conanfile.txt
```cmake
[requires]
zlib/1.2.11

[generators]
CMakeDeps
CMakeToolchain
	```

Скачать и скомпилировать пакеты
```bash
$ conan install . --output-folder=build --build=missing
```

билд проекта 
на windows
```bash
$ cd build
# assuming Visual Studio 15 2017 is your VS version and that it matches your default profile
$ cmake .. -G "Visual Studio 15 2017" -DCMAKE_TOOLCHAIN_FILE="conan_toolchain.cmake"
$ cmake --build . --config Release
```
на linux
```bash
$ cd build
$ cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release
$ cmake --build .
```
профили для компиляции. содержат параметры компиляции. такие как reliase debug..
профили находятся в папке пользователя /conan2/profiles

создать проект для debug  конфигурации
Сперва сам профиль conan-folder/profile/debug
```config
[settings]
os=Macos
arch=x86_64
compiler=apple-clang
compiler.version=14.0
compiler.libcxx=libc++
compiler.cppstd=gnu11
build_type=Debug
```


```bash
$ conan install . --output-folder=build --build=missing --settings=build_type=Debug
```

компиляция проект по windows и linux немного отличается

win
```cmake
# assuming Visual Studio 15 2017 is your VS version and that it matches your default profile
$ cd build
$ cmake .. -G "Visual Studio 15 2017" -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake
$ cmake --build . --config Debug
$ Debug\compressor.exe
Uncompressed size is: 233
Compressed size is: 147
ZLIB VERSION: 1.2.11
Debug configuration!
```

linux
```cmake
$ cd build
$ cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Debug
$ cmake --build .
$ ./compressor
Uncompressed size is: 233
Compressed size is: 147
ZLIB VERSION: 1.2.11
Debug configuration!
```