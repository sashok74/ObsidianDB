Date: 2024-04-06 - Time: 17:14
___
Tags: #QUESTION #ANSWER
___
### Question: Как установить сертификат на сайт?
### Untitled
___
### Answer:
###### Выпуск сертификата Let’s Encrypt и привязка к домену
___
### Detail:

сайт для которого выпускаем сертификат должен быть настроен через nginx. примерный конфиг get_sert.
``` nginx
server {
    listen 80;
    server_name minio.hitek-pro.ru;

   location / {
       proxy_set_header
       Host $http_host;
       proxy_pass http://192.168.7.119:9000;
       include /etc/nginx/snippets/well-known;
   }
}
```

Сначала установим утилиту Certbot, позволяющую автоматически настроить https и в дальнейшем запрашивать обновление сертификата при истечении по расписанию (на данный момент сертификат Let’s Encrypt нужно перевыпускать каждые 3 месяца). Для установки запустим команду

```
sudo apt install certbot
```

После установки Certbot, создайте файл для Let’s Encrypt для валидации домена ${webroot-path}/.well-known/acme-challenge директории. Создадим директорию и дадим Nginx права на нее

```
sudo mkdir -p /var/lib/letsencryp
sudo chmod g+s /var/lib/letsencrypt
```

Далее создаем файл с конфигурацией

sudo nano /etc/nginx/snippets/well-known

Копируем содержание в файл

```nginx
location ^~ /.well-known/acme-challenge/ {
  allow all;
  root /var/lib/letsencrypt/;
  default_type "text/plain";
  try_files $uri =404;
}
```

Добавляем в файл конфигурации /etc/nginx/sites-available/get_sert строку (в примере она уже есть)

include snippets/well-known;

Получим сертификат

```
sudo certbot certonly --agree-tos --email rolliks@gmail.com --webroot -w /var/lib/letsencrypt/ -d minio.hitek-pro.ru
```

получим вот такой вывод в случае удачи. от туда копируем пути
Successfully received certificate.
``` bash
Certificate is saved at: /etc/letsencrypt/live/minio.hitek-pro.ru/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/minio.hitek-pro.ru/privkey.pem
This certificate expires on 2024-07-05.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.
```
Подключаем сертификат. Сначала генерируем 2048 битный сертификат.

```
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048  
```
  

Редактируем конфигурационный файл nginx 
по сути добавляем сертификаты.
```nginx

server {
    listen 443 ssl http2;
    server_name minio.hetek-pro.ru;
     ...
	ssl on;
    ssl_certificate /etc/letsencrypt/live/minio.hitek-pro.ru/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/minio.hitek-pro.ru/privkey.pem;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    ssl_ciphers ‘EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH’;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    location / {
      ...
    }
}
```

при необходимости проверяя конфигурацию и делаем перевыпуск сертификата по расписанию в час ночи 1 числа каждого месяца.

```bash
sudo crontab -e
```

```c 
0 1 1 * * /usr/bin/certbot renew & > /dev/null
```
___
### Zero-Links
[00_LINUX_UTIL](../__Z_CORE/00_LINUX_UTIL.md)
___
### Links
