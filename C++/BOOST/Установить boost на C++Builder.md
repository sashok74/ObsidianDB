Date: 2023-12-16 - Time: 10:50
___
Tags: #QUESTION #ANSWER
___
### Question: как установить boost
### Untitled
___
### Answer:
Скачиваем: [include.rar (41,69 МБ)](https://href.li/?https://workupload.com/file/8Y3aMBS7LVu) (Ссылка не работает, используйте VPN)  
Извлекаем содержимое include.rar в C:\Program Files (x86)\Embarcadero\Studio\22.0\include\  
Запускаем РАД Студию  
Добавляем переменные заполнители...  
Options->IDE->Environment Variables [User System Overrides]  
CG_32_BOOST_ROOT = c:\program files (x86)\embarcadero\studio\22.0\include\boost_1_70\  
CG_64_BOOST_ROOT = c:\program files (x86)\embarcadero\studio\22.0\include\boost_1_70\  
CG_BOOST_ROOT = c:\program files (x86)\embarcadero\studio\22.0\include\boost_1_39\  
Добавляем пути переменных заполнителей...  
Options->IDE->Language->C++->Paths and Directories [ Windows 32-bit ]  
Modern C++ Compiler  
[System Include Path]  
$(CG_32_BOOST_ROOT)  
Classic Compiler  
[System Include Path]  
$(CG_BOOST_ROOT)\boost\tr1\tr1  
$(CG_BOOST_ROOT)  
Options->IDE->Language->C++->Paths and Directories [ Windows 64-bit ]  
[System Include Path]  
$(CG_64_BOOST_ROOT)\boost\tr1\tr1  
$(CG_64_BOOST_ROOT)  
Перезапускаем РАД Студию.  
Готово...
___
### Detail:

___
### Zero-Links
[00_CPP](../../__Z_CORE/00_CPP.md)
___
### Links
