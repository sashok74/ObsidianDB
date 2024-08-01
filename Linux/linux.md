поиск команды по описанию  - __apropos__ -a word1 word2

### права и пользователи  

0. информация о пользователе 			`id username`
1. список пользователей   				`sed 's/:.*//' /etc/passwd`
2. список групп           				`cat /etc/group`  
2.1. Добавить пользователя в группу		`usermoduse -a -G disks,vboxusers username (-g новая основная группа)`  
2.2. Добавить пользователя Sudoers  	`usermod -aG sudo username`  
2.2.1. Редактировать `/etc/sudoers`   
3. добавить пользователя				`adduser username`   
3.1. добавить группу					`addgroup`	
4. сменить права на фaйл                `chmod [ugo] [-+] [rwx] filename`
5. сменить владельца 					`chown -R username:groupname ~/path/dir`
6. специальные разрешения просмотр		i`find / -type f -perm -04000`
6.1. 
7. пользователи SAMBA 					`pdbedit -L`

### поиск в файлах и файлов
- исключить из поиска
   ```bash
grep --include=\*.{c,h} -rnw '/ПУТЬ/ДО/ПАПКИ/' -e "ШАБЛОН"
grep --exclude=*.o -rnw '/path/to/somewhere/' -e "pattern"
grep --exclude-dir={dir1,dir2,*.dst} -rnw '/path/to/somewhere/' -e "pattern" --color
   ```

- поиск всех каталогов и установка s-bit
   ```bash
find  /mnt/samba/ -type d -exec chmod g+s {} \;	
   ```

 - поиск всех файлов изменены за последние 50 дней и копирование на флэшку. исключая '*.db*'
   ```bash
find /srv/common/ -mtime -50 ! -path '*.db*' -print0 | xargs -0 cp --parents --target-directory /mnt/usb/ 
   ```

  - поиск каталогов у которых владелец firebird

   ```bash
find / -path /tmp -prune -o -path /proc -prune -o -type d  -group firebird -print 2>/dev/null
   ``` 

- поиск файлов по шаблону исключая директории и потом поиск в найденных по части строки.
   ```bash
find / -not \( -path '/sys*' -o -path '/mnt*' \) -name '*bash*' -exec grep -H 'tree' {} \;
   ```

- найти и вывести информацию о файлах более 50 мегабайт.
   ```bash
find / -xdev -type f -size +50M -exec ls -lSh {} \;
   ```

  Для вывода списка файлов с содержанием (значения переменных) слева для интерфейса `ens18`, вы можете использовать следующую команду:

```bash
Для вывода списка файлов с содержанием (значения переменных) слева для интерфейса `ens18`, вы можете использовать следующую команду:

```bash
for f in /proc/sys/net/ipv4/conf/ens18/*; do echo -e "$(cat $f)t$(basename $f)"; done
```
```
### Файлы
1. Дерево файлов						
   ```bash 
tree -d -L 3 -I exclude_patern
   ```
2. Размер и сумма выбранных каталогов	
   ```bash
du -chs /mnt/samba/folder1/ /mnt/samba/foldercd 2/
   ```
3. Размер папок в каталоге на глубину	
   ```bash
du -h --max-depth=1 /mnt/samba/ 
   ```
4. SAMBA открытые шары					
   ```bash
 smbstatus --shares
   ```
5. Создание символических ссылок
```shell
ln -s /home/pingvinus/myfile.txt mylink
ls -li `просмотр`
```
### ПАКЕТЫ

1. список пакетов 			dpkg-query -l 
2. список репозиториев 		/etc/apt/sources.list
3. файлы пакета             dpkg -L пакет.
4. удалить все пакеты которые содержат определенно имя -l | grep часть имени | awk '{print $2}' | xargs sudo aptitude purge -y 


###

cat /etc/passwd      -- шел который запускается при старте
cat /etc/shells      -- все шелы установленные

### переменные среды
export
env
export VARNAME=VarValue
unset VARNAME

### процессы и службы

1.  ps				-- список процессов  ps -eF;ps -e --forest
2.  ctrl-z			-- остановить процесс 
3.  fg #proc		-- вернуть остановившейся процесс
4.  bg #proc		-- продолжить выполнять процесс в бакграунде
5.  jobs			-- список процессов BG и FG 
6.  whatch			-- мониторинга процессов в реальном времени watch -n 1 'ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head'
7.  tail -f 		-- вывод начала (head) и хвоста файла
8.
10. systemctl list-unit-files			-- все службы.
11. sudo systemctl cat nginx.service 	-- файл просесса.
12.
10. kill -9 #proc	-- убить процесс

### SSH

ssh-keygen -t rsa -b 2048 -C "<comment>"
ssh-copy-id -i ~/.ssh/id_rsa.pub root@server2  (server2 root)
xclip -sel clip < ~/.ssh/id_rsa.pub копировать.>

удаляет информацию о ключах с локального ПК
ssh-keygen -f "/home/sasha/.ssh/known_hosts" -R "gitlab.skillbox.ru"

копируем ключ на сервер.

cat ~/.ssh/id_rsa.pub | 
ssh raa@medusa.lan "mkdir -p ~/.ssh 
             && touch ~/.ssh/authorized_keys 
             && chmod -R go= ~/.ssh 
			 && cat >> ~/.ssh/authorized_keys"

копируем файлы.
rsync -r -zP -e "ssh -p 1022" raa@85.202.10.174:/source/ /distination

scp -pr -P 1022 raa@85.202.10.174:/source/ /distination

Если для разных ресурсов разные ключи или параметры соединения то используем файл config
указать порт по которому будем соединяться.
```
cat ~/.ssh/config
```
```yaml
Host server1
	HostName server1.doven.ru
	User username
	IdentityFile C:\Users\a.makarenko\.ssh\id_rsa1 
	Port 22

# Default identity
Host *
	IdentityFile  C:\Users\a.makarenko\.ssh\id_rsa
```

### альтернативы

update-alternatives --get-selections

update-alternatives --config editor
update-alternatives --install /usr/bin/editor editor /usr/local/bin/nvim 50

### сеть 

hostnamectl		узнать имя компьютера.

обновить dhcp (eth0 меняем на свой. dmesg | grep eth  )
dhclient -r -v eth0 && rm /var/lib/dhcp/dhclient.* ; dhclient -v eth0
или udhcpc

ip l	имена интерфейсов

### NFS
1. установка nfs 
   ```bash    apti nfs-kernel-server   ```
2. добавить папки которые буду расшарены в файл   /etc/exports
    ```bash  /mnt/heap       192.168.7.0/24(rw,sync,no_subtree_check)    ```   
3. на клиенте установить 
     ```bash apti nfs-common```
4. посмотреть расшаренные папки
     ````bash showmount -e medusa ````
5. примонтировать на 1 раз 
     ```bash mount medusa:/heap /mnt/heap ```
6 . примонтировать при загрузке через fstab
     ```bash medusa:/mnt/heap /mnt/heap      nfs     defaults        0       0```

### зависимости 

ldd /bin/ls			-- постмотреть все зависимости выбраррной программы в данном примере - ls

### применить изменения

source ~/.bashrc
или
. ~/.bashrc

### Диски

ls -l /dev/		список дисков
mount			примонтированные разделы
df -h			подключенные диски с информацией
lsblk			более подробная инфа
lsblk -o type,kname,name,path,mountpoint,size,fsuse%,uuid,model

fdisk -l		инфорамация о размере
lvs				инфорамация о системе lvm
pvs             информация о физических томах
pvdisplay		тоже
sfdisk -l       прссмотр и редактирование партиций
blkid           UUID


lvmdiskscan

### Шедулер

crontab -e

### Графическая оболочка

aptitude -y remove xserver-xorg-core		- удаление графичкской оболочки
aptitude search '~i~Px-display-manager'		- кто запускает xserver

пример вывода:
i   xdm                                          - X display manager

update-rc.d xdm disable						- отключить, чтоб не запускался при старте 

### Cистема

systemd-cgls
systemctl status
systemctl -t slice
systemd-cgtop

### lvm 

1. Меняем размер home 
    cd /
	sudo umount -fl /home
	sudo e2fsck -f /dev/mapper/resurs--vg-home
	sudo resize2fs /dev/mapper/resurs--vg-home 500G
	sudo lvreduce -L 500G /dev/mapper/resurs--vg-home
	sudo mount /home/
	
2. Создаем диск lvm	
	sudo lvcreate -n common -L120G resurs-vg
	sudo mkfs.xfs -L common /dev/resurs-vg/common
    mkdir /srv/common
	в файле /etc/fstab добавляем строку :
	/dev/mapper/resurs--vg-common /srv/common           xfs    defaults        0       2
	mount -a

3.	
	pvdisplay --maps		- информация о lvm
4.
    операции с ексетеншинами и пустым местом:
		pvs -v --segments /dev/nvme0n1p3	--смотрим что и как
		pvmove --alloc anywhere /dev/sda5:24482-25509 /dev/sda:21457-22484	--перемещаем превый участок во второй
		pvmove --alloc anywhere /dev/sda5:24482+1000 /dev/sda:21457+1000	--тоже ну с указанием размера.
### ZFS
1. создать пул:
2. Создать valuem (блочное устройство в пуле ) :  10 гиг в пуле big-data и создаем файловую систему ext4
   ```bash
   zfs create -V 10G big-data/heap
   mkfs.ext4 /dev/zvol/big-data/heap
   ```
3. создать файловую систему. 
	1. ограничить ее по объему
	2. задать новую точку монтирования
	3. расшарить для nfs
	4. права доступа
   ```bash
   zfs create -o compression=on big-data/download
   zfs set quota=100G big-data/download
   zfs set mountpoint=/mnt/download big-data/download
   zfs set sharenfs=on big-data/download
   zfs set sharenfs='rw,no_root_squash' big-data/download
   ```

5.  Просмотр:
   ```bash
   zfs list
   zfs get guid  big-data/heap
   ```
   
### Мониторинг

	dnstop eno1 -l 7 -i 192.168.0.100	- обращения к серверу DNS
	pvs -v --segments /dev/nvme0n1p3	- cвободное место в lvm
	
	температура
	apt install lm-sensors
	> sensors
	
### Форавардинг портов

/etc/sysctl.conf
#net.ipv4.ip_forward=1, убираем знак комментария.

включить разово echo 1 > /proc/sys/net/ipv4/ip_forward

### Фильтрация трафика. netfilter

посмотреть все таблицы:

for table in filter mangle nat raw
do
    echo
    echo "Rules for table $table"
    echo
    iptables -t "$table" --line-numbers -nvL
done

### Проверьте открытые порты

sudo lsof -nP -iTCP -sTCP:LISTEN

ss -tulpn

###Торент демон
```bash
aptitude instal transmission-daemon
```





