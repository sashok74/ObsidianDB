Date: 2024-03-15 - Time: 22:58
___
Tags: #QUESTION #NO_ANSWER
___
### Question: Как подключиться по rdp к линукс
### Untitled
___
### Answer: xrdp

___
### Detail:
Обновите пакеты apt: sudo apt update 
Откройте TCP-порт 3389: sudo ufw allow 3389 
Установите xrdp: sudo apt install xrdp 
Установите пакет xorgxrdp: sudo apt install xorgxrdp 
Активируйте xrdp: sudo systemctl enable xrdp 
Проверьте статус xrdp: sudo systemctl status xrdp 
Установите графическое окружение рабочего стола Xfce: sudo apt install xfce4 
Запустите xrdp командой: sudo systemctl start xrdp 
Установите утилиты поддержки: apt install net-tools 
Узнайте ip адрес linux сервера: ifconfig
___
### Zero-Links
[00_LINUX_UTIL](../__Z_CORE/00_LINUX_UTIL.md)
___
### Links
