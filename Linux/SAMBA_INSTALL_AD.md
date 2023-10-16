Устанавливать и настраивать будем на примере Debian Buster. Пакеты будут браться из 
репозитория Debian Stable, а также компилироваться из исходников. В качестве шлюза 
будет выступать pfSense.

Будут использоваться:

    pfSense 2.4.3-RELEASE-p1
    Debian Buster | Raspbian Buster;
    .deb Samba 4.9.5;
        DNS: SAMBA_INTERNAL;
    .source Samba 4.9.17;
        DNS: SAMBA_INTERNAL;

Полезные источники:

    Samba Wiki [link];
    Все о Samba [link];
    RSAT for Windows [link];
    [Setting up Samba as an Active Directory Domain Controller]
    [Managing the Samba AD DC Service Using Systemd]
    [Setting up a Share Using Windows ACLs]
    [Libnss winbind Links]
    [Configuring Logging on a Samba Server]
    [Configure Samba to Bind to Specific Interfaces]
    [Configuring LDAP over SSL (LDAPS) on a Samba AD DC]
    [Samba AD DC Port Usage]
    [DNS Administration]

Подготовка:

Задаем имя хоста:

# hostnamectl set-hostname dc0
 - Проверяем,
# cat /etc/hostname

HOSTS:

# nano /etc/hosts
127.0.0.1       localhost
#127.0.1.1      dc0.net.lan     dc0
192.168.0.251   dc0.net.lan     dc0

Проверяем:

# hostname --fqdn
# hostname --ip-address

Настраиваем DNS-резолвер:
Важно! На этапе тестирования, снимим комментарий с "internal" и поставим на "external".

# nano /etc/resolv.conf
domain net.lan
search net.lan
#nameserver 192.168.0.251 # internal Samba DNS-Сервер;
nameserver 192.168.1.52 # external DNS-Сервер;

Сетевой интерфейс:
Чтобы в дальнейшем не возникало всевозможных проблем с сетью, зададим статические настройки 
для сетевого интерфейса.

# nano /etc/network/interfaces
iface enp0s3 inet static
address 192.168.0.251
netmask 255.255.248.0
gateway 192.168.0.6
#dns-nameservers 192.168.0.251
#dns-search net.lan
 - Команды для работы с сетевыми интерфейсами,
# service networking restart
# /etc/init.d/networking restart
# ifdown enp0s3
# ifup enp0s3
# ip a show enp0s3

Установка:

Устанавливаем необходимые пакеты:

- "Samba" - реализация протокола SMB / CIFS для Unix-систем, обеспечивающая поддержку 
межплатформенного совместного использования файлов с Microsoft Windows, OS X и другими 
Unix-системами. Samba также может функционировать как контроллер домена.

- "krb5-config" - Служит для настройки Kerberos v5, файл /etc/krb5.conf;

- "krb5-user" - Cистема аутентификации пользователей и сервисов в сети;

- "winbind" - Демон, который объединяет механизмы проверки подлинности и службы каталогов 
(пользовательский / групповой поиск) из домена Windows в системе Linux;

- "libnss-winbind" - Поиск пользователей / групп на основе Winbind через /etc/nsswitch.conf.

# apt-get install samba winbind libpam-winbind libnss-winbind libpam-krb5 krb5-config krb5-user smbclient
 - NET.LAN
 - dc0
 - dc0

Настройка домена:
В DC служба Samba автоматически запускает требуемые службы "smbd" и "winbindd" в качестве 
подпроцессов. Если мы попытаемся запустить их вручную, то Samba DC не будет работать должным 
образом. В Debian, поставщик пакетов создал дополнительные служебные файлы Samba, мы их 
отключим и замаскируем, чтобы другие службы не активировали их.

 - Маскировка и отключение служб,
# systemctl stop samba-ad-dc smbd nmbd winbind
# systemctl mask samba-ad-dc smbd nmbd winbind
# systemctl disable samba-ad-dc smbd nmbd winbind
 - Переименовываем файл, для того, чтобы samba-tool сконфигурировала новый,
# mv /etc/samba/smb.conf /etc/samba/smb.conf.old
 - Настраиваем samba ad в интерактивном режиме,
# samba-tool domain provision --use-rfc2307 --interactive
 - NET.LAN
 - NET
 - dc
 - SAMBA_INTERNAL
 - 192.168.1.52 IP-адрес DNS-сервера, на который будут перенаправлены запросы, которые 
 не может разрешить внутренний (internal) DNS-сервер Samba;
 - Аналогично и для kerberos,
# mv /etc/krb5.conf /etc/krb5.conf.old
# cp /var/lib/samba/private/krb5.conf /etc/krb5.conf
 - Включаем samba-ad-dc,
# systemctl unmask samba-ad-dc
# systemctl enable samba-ad-dc
# systemctl restart samba-ad-dc

Тестирование Samba AD DC:

 - Проверяем режим работы AD DC,
# samba-tool domain level show
 - Verifying the File Server,
# smbclient -L localhost -U%
 - Verifying Kerberos,
# kinit administrator
 - Verifying DNS,
# host -t SRV _ldap._tcp.net.lan.
# host -t SRV _kerberos._udp.net.lan.
# host -t A net.lan.
# host -t PTR 192.168.0.251
 - List the cached Kerberos tickets,
# klist

Создаем зону обратного просмотра:
Т.к. она не создается автоматически.

# samba-tool dns zonecreate net.lan 0.168.192.in-addr.arpa
 - Добавляем запись для сервера,
# samba-tool dns add net.lan 0.168.192.in-addr.arpa 251 PTR dc0.net.lan

Configs:

Конфигурационные файлы после установки.

krb5.conf:

# nano /etc/krb5.conf
[libdefaults]
        default_realm = NET.LAN # Имя домена;
        dns_lookup_realm = false # Отключаем поиск kerberos-имени домена через DNS;
        dns_lookup_kdc = true # Оставляем (или включаем) поиск kerberos-настроек домена через DNS;

smb.conf:

# nano /etc/samba/smb.conf
[global]
        dns forwarder = 192.168.1.52
        netbios name = DC0
        realm = NET.LAN
        server role = active directory domain controller
        workgroup = NET
        idmap_ldb:use rfc2307 = yes

[netlogon]
        path = /var/locks/sysvol/net.lan/scripts
        read only = No

[sysvol]
        path = /var/locks/sysvol
        read only = No

nsswitch.conf:

# nano /etc/nsswitch.conf
passwd:         files
group:          files
shadow:         files
gshadow:        files

hosts:          files mymachines dns myhostname
networks:       files

protocols:      db files
services:       db files
ethers:         db files
rpc:            db files

netgroup:       nis

File Server:

Добавление Winbind в качестве источника пользователей и групп:
Для того, чтобы ОС прозрачно работала с пользователями, (чтобы можно было назначать 
пользователей домена владельцами папок и файлов), необходимо указать ОС использовать
 Winbind как дополнительный источник информации о пользователях и группах.

# nano /etc/nsswitch.conf
passwd:         files winbind
group:          files winbind

Setting up a Share Using Windows ACLs: [link];

# nano /etc/samba/smb.conf
[global]
	# Extended ACL Support
        vfs objects = acl_xattr
        map acl inherit = yes
        store dos attributes = yes

[share]
        path = /home/share
        read only = no
        browsable = yes
 - Перечитаем настройки,
# smbcontrol all reload-config

 - Только пользователи и группы, имеющие предоставленную привилегию SeDiskOperatorPrivilege, 
 могут настраивать разрешения на доступ к ресурсам.
# net rpc rights grant "NET\Domain Admins" SeDiskOperatorPrivilege -U "NET\administrator"

# mkdir /home/share
# chown -R root:"Domain Admins" /home/share
# chmod -R 770 /home/share

Source Compile:

Этот способ подойдет как для обычной ОС Debian Stretch, так и для Raspberry Pi mod 3b "*" 
c ее Raspbian Stretch ОС.

Устанавливаем необходимые пакеты:

# apt install acl attr autoconf bind9utils bison build-essential \
debhelper dnsutils docbook-xml docbook-xsl flex gdb libjansson-dev krb5-user \
libacl1-dev libaio-dev libarchive-dev libattr1-dev libblkid-dev libbsd-dev \
libcap-dev libcups2-dev libgnutls28-dev libgpgme-dev libjson-perl \
libldap2-dev libncurses5-dev libpam0g-dev libparse-yapp-perl \
libpopt-dev libreadline-dev nettle-dev perl perl-modules pkg-config \
python-all-dev python-crypto python-dbg python-dev python-dnspython \
python3-dnspython python-markdown python3-markdown \
python3-dev xsltproc zlib1g-dev liblmdb-dev lmdb-utils libtasn1-bin
 - NET.LAN
 - dc0
 - dc0

Samba install:

# wget https://download.samba.org/pub/samba/stable/samba-4.9.17.tar.gz
# tar -zxf samba-*
# cd samba-4.9.17/
 - Конфигурация,
# ./configure --help
# ./configure --bindir=/usr/local/bin --sbindir=/usr/local/sbin \
--sysconfdir=/etc/samba --localstatedir=/var --libdir=/usr/local \
--datarootdir=/usr/share/samba --mandir=/usr/share/man --with-systemd \
--with-logfilebase=/var/log/samba --enable-selftest
#--systemd-install-services --with-systemddir=/etc/systemd/system - Не работают.
 - Собираем,
# make -jN # N = кол-во потоков +1 # cat /proc/cpuinfo
 - Устанавливаем,
# make -jN install
 - или собрать пакет, а затем установить,
# checkinstall --install
 - Управление пакетами,
# dpkg --install samba_4.9.17-1_amd64.deb
# dpkg --remove samba

Замаскировать пакеты:
Чтобы не установились пакеты самбы из репозитория (более старые версии), при выходе апдейтов безопасности и новых версий.

# apt-mark hold libwbclient0 samba-common
# apt-mark unhold libwbclient0 samba-common

Systemd:
Настраиваем сервис.

# nano /etc/systemd/system/samba-ad-dc.service
[Unit]
Description=Samba Active Directory Domain Controller
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/local/sbin/samba -D
PIDFile=/var/run/samba.pid
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
# systemctl daemon-reload

Настройка домена:
В случае с ОС Raspbian Stretch, потребуется выполнить # mv /etc/samba/smb.conf /etc/samba/smb.conf.old

 - Маскировка и отключение служб,
# systemctl mask smbd nmbd winbind (new version's 4.10.X? smb nmb)
# systemctl disable smbd nmbd winbind (new version's smb nmb)

 - Настраиваем samba ad в интерактивном режиме: (переименовываем файл, для того, чтобы samba-tool сконфигурировала новый)
# mv /etc/krb5.conf /etc/krb5.conf.old
 - интерактивный,
# samba-tool domain provision --use-rfc2307 --interactive
 - ручной,
# samba-tool domain provision --use-rfc2307 --realm=NET.LAN --domain=INT --server-role=dc --dns-backend=SAMBA_INTERNAL --adminpass=pass
# cp /usr/local/samba/private/krb5.conf /etc/krb5.conf

- Включаем samba-ad-dc
# systemctl enable samba-ad-dc
# systemctl start samba-ad-dc

Libnss winbind Links: [link]
Чтобы хосты могли получать информацию о пользователях и группах из домена с помощью Winbind.

 - Debian-based Operating Systems
x86_64:
# ln -s /usr/local/libnss_winbind.so.2 /lib/x86_64-linux-gnu/
# ln -s /lib/x86_64-linux-gnu/libnss_winbind.so.2 /lib/x86_64-linux-gnu/libnss_winbind.so
ARM:
# ln -s /usr/local/libnss_winbind.so.2 /lib/arm-linux-gnueabihf/
# ln -s /lib/arm-linux-gnueabihf/libnss_winbind.so.2 /lib/arm-linux-gnueabihf/libnss_winbind.so

Готово. Далее переходим к разделу "Тестирование Samba AD DC:".

    Если samba устанавливается на малинку, то

    - Через raspi-config не трогать N3 Network interface names, в логах самбы будет ругаться на невозможность найти сетевой интерфейс.

    - Не нужно устанавливать через чекинстал, куча конфликтов при установке других пакетов;

    - Все настройки сети малинка должна получить от pfsense по dhcp. иначе после рестарта не подключиться.

pfSense:

Особенности использования и настройки:

Static IP-Address:

Status -> DHCP Leases -> Leases - Actions -> + (Add static mapping)
# Static DHCP Mapping on (*Lan Interface)
MAC Address - xx:xx:xx:xx:xx:xx
IP Address - 192.168.5.10
Hostname - rp3
Description - Server
DNS Servers - 192.168.0.251 192.168.1.52
 - Save.
 - Добавить/Просмотреть статические IP-адреса можно в,
Services -> DHCP Server -> V5_INT - (*Lan Interface)
# DHCP Static Mappings for this Interface

DNS Resolver:
Чтобы в свойствах сетевого подключения, на машине с Windows, вручную не прописывать статический DNS-Server 192.168.5.10, или например, не выдавать этот же IP-адрес DHCP-сервером.

Services -> DNS Resolver -> General Settings - > Domain Overrides -> Add
# Domains to Override with Custom Lookup Servers
Domain - net.lan
IP Address - 192.168.0.251
Description - Samba AD DC
 - Save.