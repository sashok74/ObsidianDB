Date: 2025-01-28 - Time: 14:25
___
Tags: #QUESTION #NO_ANSWER
___
### Question: 
 
### Как защитится от атак на mikrotik?
___
### Answer:
скрипт, аналог file2ban
___
### Detail:

#### Блокировка IP-адресов из черного списка для SSH
add chain=input protocol=tcp dst-port=22 src-address-list=ssh_blacklist action=drop comment="Block SSH brute force attackers" disabled=no

#### Перемещение IP-адресов из stage3 в ssh_blacklist на 10 дней
add chain=input protocol=tcp dst-port=22 connection-state=new src-address-list=ssh_stage3 action=add-src-to-address-list address-list=ssh_blacklist address-list-timeout=10d comment="Move to SSH blacklist after stage3" disabled=no

#### Перемещение IP-адресов из stage2 в ssh_stage3 на 30 минут
add chain=input protocol=tcp dst-port=1022 connection-state=new src-address-list=ssh_stage2 action=add-src-to-address-list address-list=ssh_stage3 address-list-timeout=30m comment="Move to SSH stage3 after stage2" disabled=no

#### Перемещение IP-адресов из stage1 в ssh_stage2 на 10 минут
add chain=input protocol=tcp dst-port=1022 connection-state=new src-address-list=ssh_stage1 action=add-src-to-address-list address-list=ssh_stage2 address-list-timeout=10m comment="Move to SSH stage2 after stage1" disabled=no

#### Добавление IP-адресов в ssh_stage1 на 1 минуту при первой попытке
add chain=input protocol=tcp dst-port=1022 connection-state=new action=add-src-to-address-list address-list=ssh_stage1 address-list-timeout=1m src-address-list=!white_list comment="Add to SSH stage1 on first attempt" disabled=no


/ip firewall filter

#### Блокировка IP-адресов из черного списка для Winbox
add chain=input protocol=tcp dst-port=8291 src-address-list=winbox_blacklist action=drop comment="Block Winbox brute force attackers" disabled=no

#### Перемещение IP-адресов из stage3 в winbox_blacklist на 10 дней
add chain=input protocol=tcp dst-port=8291 connection-state=new src-address-list=winbox_stage3 action=add-src-to-address-list address-list=winbox_blacklist address-list-timeout=10d comment="Move to Winbox blacklist after stage3" disabled=no

#### Перемещение IP-адресов из stage2 в winbox_stage3 на 30 минут
add chain=input protocol=tcp dst-port=8291 connection-state=new src-address-list=winbox_stage2 action=add-src-to-address-list address-list=winbox_stage3 address-list-timeout=30m comment="Move to Winbox stage3 after stage2" disabled=no

#### Перемещение IP-адресов из stage1 в winbox_stage2 на 10 минут
add chain=input protocol=tcp dst-port=8291 connection-state=new src-address-list=winbox_stage1 action=add-src-to-address-list address-list=winbox_stage2 address-list-timeout=10m comment="Move to Winbox stage2 after stage1" disabled=no

#### Добавление IP-адресов в winbox_stage1 на 1 минуту при первой попытке
add chain=input protocol=tcp dst-port=8291 connection-state=new action=add-src-to-address-list address-list=winbox_stage1 address-list-timeout=1m src-address-list=!white_list comment="Add to Winbox stage1 on first attempt" disabled=no

правила желательно поднять перед правилом которое разрешает порты.
на которые настроен скрипт.

___


### Zero-Links

___
### Links
