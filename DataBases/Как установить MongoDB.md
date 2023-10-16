Date: 2023-09-24 - Time: 22:55
___
Tags: #QUESTION #NO_ANSWER
___
### Question: Как установить MongoDB
### Установка на linux  и Windows
___
### Answer:
1. 
```bash
     aptitude update 
```
2. 
```bash  
    aptitude install gnupg curl
```
3. импортируем MongoDB public GPG key берем для нужной версии [https://pgp.mongodb.com](https://pgp.mongodb.com/)
```bash
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | \
sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
--dearmor
aptitude update
```
4. Создайте файл списка с помощью команды, соответствующей вашей версии Debian:
```bash
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] http://repo.mongodb.org/apt/debian bullseye/mongodb-org/7.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
5. Установим последнюю версию
 ```bash
aptitude install -y mongodb-org
```  
6. расположение файлов
```
- its data files in `/var/lib/mongodb`
    
- its log files in `/var/log/mongodb`
```
7. 
```bash
systemctl start mongod
systemctl enable mongod
```
___
### Detail:
# MongoDB 4 - установка на Debian 11 bullseye

Перейти к:[навигация](https://wiki.iphoster.net/wiki/MongoDB_4_-_%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%BD%D0%B0_Debian_11_bullseye#mw-navigation), [поиск](https://wiki.iphoster.net/wiki/MongoDB_4_-_%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%BD%D0%B0_Debian_11_bullseye#p-search)

[](https://iphoster.net/pl.php?34838 "Доступная цена")

### MongoDB 4 - установка на Debian 11 bullseye

Особенность установки - это и**спользование репозитория от Debian 10 buster**, так как официально нет репозитория для 11 версии Debian-а.

**Алгоритм установки mongodb 4.4 на Debian 11 bullseye**:

 apt install sudo curl wget gnupg -y
 wget -qO - [https://www.mongodb.org/static/pgp/server-4.4.asc](https://www.mongodb.org/static/pgp/server-4.4.asc) | sudo apt-key add -
 echo "deb http://repo.mongodb.org/apt/debian buster/mongodb-org/4.4 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
 apt update
 apt-get install -y mongodb-org
 systemctl enable mongod
 systemctl start mongod

  
Проверка версии MongoDB:

 mongod --version
db version v4.4.13
Build Info: {
   "version": "4.4.13",
   "gitVersion": "df25c71b8674a78e17468f48bcda5285decb9246",
   "openSSLVersion": "OpenSSL 1.1.1n  15 Mar 2022",
   "modules": [],
   "allocator": "tcmalloc",
   "environment": {
       "distmod": "debian10",
       "distarch": "x86_64",
       "target_arch": "x86_64"
   }
}

___
### Zero-Links
[00_MONGODB](../__Z_CORE/00_MONGODB.md)
___
### Links
[Установка MongoDB на Debian](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-debian/)