Установка демона APC:
sudo apt-get install apcupsd

Для снижения количества неудачных настроек для начала найдем порт ttyS:
dmesg | grep tty

Редактируем /etc/apcupsd/apcupsd.conf
sudo nano /etc/apcupsd/apcupsd.conf
UPSNAME resurs-ups
UPSCABLE smart
UPSTYPE apcsmart   

vim /etc/default/apcupsd
ISCONFIGURED=yes

systemctl restart apcupsd.service

/sbin/apcaccess посмотреть параметры

Исправляем выключение на остановку (halt).

Открываем 

# mcedit /etc/apcupsd/apccontrol 

и находим:

doshutdown)
echo «UPS ${2} initiated Shutdown Sequence» | ${WALL}
${SHUTDOWN} -h now «apcupsd UPS ${2} initiated shutdown» 

и заменяем в ней «-h» на «-H»

doshutdown)
echo «UPS ${2} initiated Shutdown Sequence» | ${WALL}
${SHUTDOWN} -H now «apcupsd UPS ${2} initiated shutdown» 

Теперь компьютер будет получать команду shutdown -H now, что и есть режим остановки (halt).

в биосе компьютера выставляем Management — > AC PWR Loss Restart