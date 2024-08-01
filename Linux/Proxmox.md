Date: 2024-03-17 - Time: 21:08
___
Tags: #PROXMOX
___
# Возникшие вопросы и ответы.
___ 
### Shot Descripion:
proxmox 
___
### Main info:
1. Как подключить диск. например с гипервизора?
	1.  Вариант через NFS 
	2.  Вариант .. подключить диск через конфиг.
```bash
   nano /etc/pve/qemu-server/Имя конфига.conf 
   
```
добавляем строку для диска
scsi2: /dev/zvol/big-data/db,format=raw
___
### Zero-Links
[00_SYSTEM](../__Z_CORE/00_SYSTEM.md)
[00_LINUX_UTIL](../__Z_CORE/00_LINUX_UTIL.md)
___
### Links

