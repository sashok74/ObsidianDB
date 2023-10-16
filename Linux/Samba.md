bind9
имя сервера 
установить hostnamectl set-hostname  name
сеть 
vim /etc/hostname
vim /etc/network/interfaces 	//(настройки сети статические)

ip l	имена интерфейсов берем отсюда
пример:
# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eno1
iface eno1 inet static
address 192.168.100.10
netmask 255.255.255.0
broadcast 193.168.100.255
gateway 192.168.100.1
dns-nameservers 8.8.4.4 8.8.8.8

systemctl status networking
systemctl restart networking

dmesg | grep eth  				//выводит список сетевых интерфейсов
lspci | grep Ethernet 			//информация о адаптере, мак адрес здесь же

-testparm -s                    //проверить конфиг
systemctl restart  nmbd smbd 	//рестарт
-hsystemctl status smbd nmbd 		//просмотр состояния служб
ufw allow Samba !!!  			//открыть порты в фаерволе.

настраиваем самбу

-smbclient -L hostname -Uroot%12345
-sudo nmapiap -p 139 -sT "192.168.100.6"

настроить windows (smb1 чтобы видеть в сетевом окружении)
Включение SMB 1.0 в Windows 10
Win+R -- appwiz.cpl -- "удаление программ". 
"Включение или отключение компонентов"
"Поддержка общего доступа к файлам SMB 1.0/CIFS" 

можно проверить доступность ресурсов по самюе на винде.
пользователи на винде. net user или текущий  $env:UserName (в винтерм)

smbclient -L hostname -U username		// статус 
smbclient //host/share -U username%pass	// подключить шару на винде

sudo nmap -p 139 -sT "192.168.100.6"

мапим виндовую нару на линуксе
mount -v -t cifs //<IP>/MyShare /mnt -o username=Administrator,vers=2.0

