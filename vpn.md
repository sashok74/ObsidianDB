### Настройка OpenVPN-сервера на роутерах Mikrotik
в командной строке микротика.

1. подготовка.
 
/system clock
set time-zone-autodetect=no time-zone-name=Europe/Moscow

/system ntp client
set enabled=yes primary-ntp=185.209.85.222 secondary-ntp=37.139.41.250

2.  сертрификаты
    2.1. корневой:
/certificate 
add name=ca country="RU" state="31" locality="BEL" organization="Interface LLC" unit="IT" common-name="ca" key-size=2048 days-valid=3650 key-usage=crl-sign,key-cert-sign
sign ca ca-crl-host=127.0.0.1

    2.2. сертификат и закрытый ключ сервера.
/certificate 
add name=ovpn-server country="RU" state="31" locality="BEL" organization="Interface LLC" unit="IT" common-name="ca" key-size=2048 days-valid=3650 key-usage=digital-signature,key-encipherment,tls-server
sign ovpn-server ca="ca"

    2.3. клиентские сертификаты (отдаем клиенту)
/certificate 
add name=mikrotik country="RU" state="31" locality="BEL" organization="Interface LLC" unit="IT" common-name="ca" key-size=2048 days-valid=365 key-usage=tls-client
sign mikrotik ca="ca"

    2.4. экспорт сертификата.
/certificate
export-certificate mikrotik type=pkcs12 export-passphrase=12345678

В данном случае мы использовали парольную фразу 12345678. 
Экспортированные сертификаты можно скачать в разделе Files. 
  
3. Настройка OpenVPN сервера

исходим из того что у меня роутер 192.168.0.1

/ip pool
add name=ovpn_pool0 ranges=192.168.1.2-192.168.1.100  

/ppp profile
add local-address=192.168.1.1 name=ovpn remote-address=ovpn_pool0
(local-address=192.168.1.1 именно 1 подсеть. основная 0. на этот адрес потом настраивается маршрут)

убедимся, что включена аутентификация по пользователю
/ppp aaa
set accounting=yes

создадим учетные записи для клиентов. 
/ppp secret
add name=mikrotik password=123 profile=ovpn service=ovpn
В данном случае мы создали запись для пользователя mikrotik с паролем 123. 
(совпадет с именем сертификата для удобства)

включим службу open-vpn и настраиваетм параметры
/interface ovpn-server server
set auth=sha1 certificate=ovpn-server cipher=aes256 default-profile=ovpn enabled=yes require-client-certificate=yes

откроем порт
/ip firewall filter
add action=accept chain=input  dst-port=1194 protocol=tcp 

4. Настройка стандартного клиента OpenVPN на ПК

#C:\Users\sash_\OpenVPN\config\resurs.ovpn
client
dev tun
proto tcp
remote 85.202.10.174 1194
persist-key
persist-tun
pkcs12 C:\\Users\\sash_\\OpenVPN\\resurs-keys\\raa-sert.p12
auth-user-pass C:\\Users\\sash_\\OpenVPN\\resurs-keys\\auth.cfg
askpass C:\\Users\\sash_\\OpenVPN\\resurs-keys\\keypass.cfg
remote-cert-tls server

### маршрут означает что входим в подсеть 192.168.0.0
 с адреса 192.168.1.1 это local-address в профиле 
route 192.168.0.0 255.255.255.0 192.168.1.1

### шифрование которое установили в настройках open-vpn сервера
cipher AES-256-CBC 
ping 10
#comp-lzo
verb 4

в файлах
raa-sert.p12
сертификат который скачали с серевера.

auth.cfg
имя ползователя
пароль

keypass.cfg
пароль которым закрыли архив при экспорте сертификатов