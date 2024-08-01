Date: 2024-07-15 - Time: 08:41
___
Tags: #QUESTION #ANSWER
___
### Question: Как установить библиотеку google protobuf
### Untitled
___
### Answer: 
[protobuf](https://protobuf.dev/getting-started/cpptutorial/)
___
### Detail:
1. Нативная строка компиляции MS обычно где-то здесь [x86 Native Tools Command Prompt for VS 2022 Preview]("C:\Program Files\Microsoft Visual Studio\2022\Preview\VC\Auxiliary\Build\vcvars32.bat")
2. Установить гулевский abseil, легко ставится cmake
```
cmake -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=C:/Libraries/abseil/Debug ../..  

cmake --build . --target install

```
3. Клонируем исходники. ставим cmake. 
```
git clone  --recurse-submodules https://github.com/protocolbuffers/protobuf.git
```
3. cmake -G "Ninja" -DMAKE_BUILD_TYPE=Release -DMAKE_INSTALL_PREFIX="C:/Libraries/protobuf/release" ../..
### Zero-Links
[00_CPP](../__Z_CORE/00_CPP.md)
___
### Links
