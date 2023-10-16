Date: 2023-03-26 - Time: 20:13
___
Tags: #QUESTION #NO_ANSWER
___
### Question:
### Установка торрент клиента linux
___
### Answer:
transmission-daemon
___
### Detail:
```shell
aptitude instal transmission-daemon
sudo adduser torrent_server
usermod -aG debian-transmission torrent_server                          
mkdir -m 775 /srv/share/torrents                                        
mkdir -m 775 /srv/share/downlads                                        
chown torrent_server:torrent_server /srv/share/downlads                 
chown torrent_server:torrent_server /srv/share/torrents/                                                                 
cp -R /etc/transmission-daemon /home/torrent_server/.config/           
chmod -R 775 /home/torrent_server/.config/
```
`vi /etc/default/transmission-daemon` 
CONFIG_DIR="/home/torrent_server/.config/transmission-daemon/settings.json"
сменить пользователя от которого запускается служба.
```shell
systemctl edit --runtime transmission-daemon.service
```
[Service]
User=
User=torrent_server 
или
```shell
nano /etc/systemd/system/multi-user.target.wants/transmission-daemon.service
systemctl daemon-reload
systemctl restart transmission-daemon.service
```

Файл инициализирующий старт демона – `/etc/init.d/transmission-daemon`
Файл конфигурации – `/etc/default/transmission-daemon`
Файл global settings – `/etc/transmission-daemon/settings.json`
Файл local settings – `~/.config/transmission-daemon/settings.json`

___
### Zero-Links
[00_LINUX_UTIL](../__Z_CORE/00_LINUX_UTIL.md)
___
### Links
[Установка и настройка transmission-daemon](https://habr.com/ru/post/658463/)