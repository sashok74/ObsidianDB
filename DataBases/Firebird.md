### fb

инструкция установки из исходников:
git clone https://github.com/FirebirdSQL/firebird.git
aptitude install libtool libicu-dev libtommath-dev libtomcrypt-dev libncurses5-dev automake autoconf m4 make cmake zlib1g-dev unzip gcc g++ --add-user-tag util 


https://github.com/FirebirdSQL/firebird.git

установка.

если не работаю свои udf то проверить зависимости с помощью
```bash
root@planar8:/opt/firebird/UDF# ldd rfunc.so
        linux-vdso.so.1 (0x00007fff03ff8000)
        libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fe3959ff000)
        libib_util.so => not found
        libfbclient.so.2 => not found
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fe39581e000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fe395af9000)
```

если не запускается 2.5 то помнить, что его запускает xinetd
а он может быть не установлен. установить и проверить конфиг.
```bash
systemctl status xinetd.service
nano /etc/inetd.conf
```
в нано видим:
```nano
gds_db  stream  tcp     nowait      firebird /opt/firebird/bin/fb_inet_server fb_inet_server
```
### isql
 connect security.db user SYSDBA;
 CREATE USER SYSDBA PASSWORD 'm8ku234pp';
 exit;
 
### firebird.conf

```
UdfAccess = Restrict UDF
DefaultTimeZone = Asia/Yekaterinburg	

AuthServer = Srp256, Srp, Win_Sspi, Legacy_Auth #Windows clients
AuthClient = Srp256, Srp, Win_Sspi, Legacy_Auth #Windows clients
UserManager = Srp, Legacy_UserManager
WireCrypt = Enabled

instsvc install -n fb40
instsvc start -n fb40

```bash
>gbak -b -g -user SYSDBA -password planomer -se service_mgr /var/lib/firebird/2.5/system/security2.fdb /var/lib/firebird/2.5/backup/security2.fbk
scp /var/lib/firebird/2.5/backup/security2.fbk raa@sea:~/security2.fbk
root@sea:/var/backup# mv /home/raa/security2.fbk /var/backup/sec25/security2.fbk
```

```bash
/usr/local/firebird/bin/gbak -c -user SYSDBA -password planomer -se localhost/3050:service_mgr /var/backup/sec25/security2.fbk /var/data/security2db.fdb -v
```

заполняем базу пользователей легаси списка.

```bash
/usr/local/firebird/bin/isql -i /var/backup/sec25/copy_security2.sql -u SYSDBA -p planomer -ch UTF8 -e /usr/local/firebird/security5.fdb
```