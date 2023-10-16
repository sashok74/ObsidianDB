```bash
su -l
```
Password:
```bash
apt install aptitude
aptitude update
aptitude install sudo
/usr/sbin/visudo  (raa     ALL=(ALL:ALL) ALL)
sudo -i 
```
если не работают русские буквы. - dpkg-reconfigure locales
```bash
aptitude install mc htop tmux git tree npm
```
(конфиги для tmux и bash ) (для всех /etc/tmux.conf /etc/bash.bashrc)  
(копируем ключи ssh если надо SSH-Copy-ID)
(настройка SSH /etc/ssh/sshd_config)
```bash 
timedatectl set-timezone Asia/Yekaterinburg
timedatectl set-ntp true
```
(в локалке)echo 'NTP=192.168.1.251' |
```bash 
sudo tee -a /etc/systemd/timesyncd.conf > /dev/nul
timedatectl set-ntp true
systemctl restart systemd-timedated`
```

```bash
root@sea:/ aptitude install neovim #(лучше из исходников)
```
```bash
root@sea: aptitude install samba smbclient cifs-utils
```
(настраиваем самбу для работы как обычная файловая помойка)
```bash
aptitude install vsftpd
```
ftpd для переноса файлов по сети.

установка firebird из исходника.



##### GRUB
```bash
grub> ls  #- все диски.
grub> ls (hdX, Y)/ #- посматриваем содержимое всех и находим grub
grub> set root=(hdX, Y)
grub> set prefix=(hdX, Y)/...../grub
grub> insmod normal
grub> normal
```

```bash
aptitude install grub-efi
update-grub
grub-install --efi-directory=/boot/efi/
```
