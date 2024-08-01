Date: 2024-07-24 - Time: 14:52
___
Tags: #QUESTION #NO_ANSWER
___
### Question: как удобнее использовать api firebird?
### Untitled
___
### Answer: Используем генератор интерфейсов cloop

___
### Detail:
```shell
cloop.exe FirebirdInterface.idl c++ FB_API.h FB_CPP_API_H FB I
```
исходники cloop находятся в репозитории firebird
надо его скомпилировать и закинуть туда где находится FirebirdInterface.idl
далее как в командной строке
C:\repo\firebird\extern\cloop\src\cloop
___
### Zero-Links
[00_CPP](../__Z_CORE/00_CPP.md)
___
### Links
[cloop](https://github.com/FirebirdSQL/firebird/tree/master/extern/cloop)