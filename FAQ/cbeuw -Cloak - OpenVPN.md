pDate: 2023-04-21 - Time: 22:48
___
Tags: #QUESTION #NO_ANSWER
___
### Question:
### cbeuw -Cloak - OpenVPN как установить.
___
### Answer:
cbeuw -Cloak - обфускатор трафика
OpenVPN - vpn server and client
___
### Detail:
[cbeuw -Cloak/Cloak](https://github.com/cbeuw)
[скрипт для установки open-vpn](https://github.com/angristan/openvpn-install)
[скрипт для установки cloac + shadosocks](https://github.com/HirbodBehnam/Shadowsocks-Cloak-Installer)
далее добавляем клиента с помощью этого же скрипта
 и правим конфиги openvpn на сервере, чтобы он слушал cloak
``` shell
nano /etc/openvpn/server/server.conf
```
 
``` c
local 45.82.14.70  !!! 127.0.0.1                                                                                                                                                                                                                                                            port 1194          !!! 11941                                                                                                                                                                                                                                                         proto udp                                                                                                                                                                                                                                                                    dev tun
...
```

``` bash
nano /etc/cloak/ckserver.json
```

``` json
{                                                                                                                                                                                                                                                                              "ProxyBook": {                                                                                                                                                                                                                                                                 "panel": [                                                                                                                                                                                                                                                                     "tcp",                                                                                                                                                                                                                                                                       "127.0.0.1:0"                                                                                                                                                                                                                                                              ],                                                                                                                                                                                                                                                                           "shadowsocks": [                                                                                                                                                                                                                                                               "tcp",                                                                                                                                                                                                                                                                       "127.0.0.1:53715"                                                                                                                                                                                                                                                          ],                                                                                                                                                                                                                                                                           "openvpn": [                                                                                                                                                                                                                                                                   "udp",                                                                                                                                                                                                                                                                       "127.0.0.1:11941"                                                                                                                                                                                                                                                          ]                                                                                                                                                                                                                                                                          },  
```
на клиенте правим рабочий конфиг который подключался без клоака:
``` c
client
dev tun
proto udp                !!! везде udp
remote 45.82.14.70 1194  !!! 127.0.0.1 1984
resolv-retry infinite
!!! route <actual IP of the remote server> 255.255.255.255 net_gateway
nobind
```


___
### Zero-Links
[00_LINUX_UTIL](../__Z_CORE/00_LINUX_UTIL.md)
___
### Links
