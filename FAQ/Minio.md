Date: 2024-04-07 - Time: 21:10
___
Tags: #MINIO
___
# Как установить Minio на Debian
___ 
### Shot Descripion:
Начать нужно с описания на сайте:

[Single-Node Single-Drive MinIO](https://min.io/docs/minio/linux/operations/install-deploy-manage/deploy-minio-single-node-single-drive.html)
___
### Main info:
Диск для файлов примонтировал с гипервизора. где его создал как блочное устройство.э

создал диск(не файловую систему) в zfs и отформатировал в xfs как просят в доке minio

в конфиг вируталки добавил
```
nano /etc/pve/qemu-server/106.conf
chmod -R u+rwX,g+rwX,o-rwx /mnt/minio
```

blkid - посмотреть uuid блочного устройства
примонтировал через /etc/fstab
```
UUID=6df61b6c-a23e-4859-9f62-9e37bcac6899       /mnt/minio      xfs     defaults        0       2
```

пользователь
```bash
groupadd -r minio-user
useradd -M -r -g minio-user minio-user
chown minio-user:minio-user /mnt/minio
chmod -R u+rwX,g+rwX,o-rwx /mnt/minio
```

скачал 
wget https://dl.min.io/server/minio/release/linux-amd64/archive/minio_20240330094156.0.0_amd64.deb -O minio.deb
запускаем
```bash
sudo dpkg -i minio.deb
```

Создаем(проверяем наличие) файл systemd:
/etc/systemd/system/minio.service
> 

Создаем(проверяем наличие) файла с переменными окружения для службы MinIO. 
/etc/default/minio:
это важный файл:
```
MINIO_ROOT_USER=root
MINIO_ROOT_PASSWORD=root_1234567890

MINIO_VOLUMES="/mnt/minio"
# разспололжение сертификатов. 
# для входа в консоль по hhtps
# порты констоли и апи
MINIO_OPTS="--certs-dir /etc/minio/certs -C /etc/minio --address :9000 --console-address :9001"

# есил сервер снаружи. то это путь который будет
# подставляься в ссылки.  
# второй путь это откуда вход в конслоь. очень важно 
# Чтобы они совападал. короче с ними вся возня.

MINIO_SERVER_URL="https://minio.hitek-pro.ru"
MINIO_BROWSER_REDIRECT_URL="https://minio.hitek-pro.ru/minio/ui/"
```


Запускаем службу:

systemctl start minio.service

Проверяем успешных запуск и наличие ошибок:

systemctl status minio.service

Если ошибок нет и статус корректный, переходим в WEB-часть для работы: http://minio1.example.net:9001

для автоматического старта
sudo systemctl enable minio.service

для входа в панель по http надо создать самподписной сертификат как написано на сайте. не забыть про путь до сертификатов.
это для шифрования трафика не для сайта.
[# Network Encryption (TLS)](https://min.io/docs/minio/linux/operations/network-encryption.html)
You can use the MinIO [certgen](https://github.com/minio/certgen) to mint self-signed certificates for evaluating MinIO with TLS enabled. For example, the following command generates a self-signed certificate with a set of IP and DNS Subject Alternate Names (SANs) associated to the MinIO Server hosts:

certgen -host "localhost,minio-*.example.net"

Place the generated `public.crt` and `private.key` into the `/path/to/certs` directory to enable TLS for the MinIO deployment. Applications can use the `public.crt` as a trusted Certificate Authority to allow connections to the MinIO deployment without disabling certificate validation.
как добавить сертификаты здесь
[Выпуск сертификата Let’s Encrypt](Выпуск%20сертификата%20Let’s%20Encrypt.md)

настройка nginx разделить можно апи и панель можно путями а можно поддоменами. вот вариант с путями.
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name minio.hitek-pro.ru;
    return 301 https://$server_name$request_uri;
}

# minio API
upstream minio_s3 {
   least_conn;
   server minio.hitek-pro.ru:9000;
}

# webUI
upstream minio_console {
   least_conn;
   server minio.hitek-pro.ru:9001;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name minio.hetek-pro.ru;
    access_log /var/log/nginx/minio.dev-ssl-access.log;
    error_log /var/log/nginx/minio.dev-ssl-error.log;

    ssl on;
    ssl_certificate /etc/letsencrypt/live/minio.hitek-pro.ru/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/minio.hitek-pro.ru/privkey.pem;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_dhparam /etc/ssl/certs/dhparam.pem;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;

   # Allow special characters in headers
   ignore_invalid_headers off;
   # Allow any size file to be uploaded.
   # Set to a value such as 1000m; to restrict file size to a specific value
   client_max_body_size 0;
   # Disable buffering
   proxy_buffering off;
   proxy_request_buffering off;

   location / {
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_connect_timeout 300;
      # Default is HTTP/1, keepalive is only enabled in HTTP/1.1
      proxy_http_version 1.1;
      proxy_set_header Connection "";
      chunked_transfer_encoding off;

      proxy_pass https://minio_s3; # This uses the upstream directive definition to load balance
   }

   location /minio/ui/ {
      rewrite ^/minio/ui/(.*) /$1 break;
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-NginX-Proxy true;

      # This is necessary to pass the correct IP to be hashed
      real_ip_header X-Real-IP;

      proxy_connect_timeout 300;

      # To support websockets in MinIO versions released after January 2023
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      # Some environments may encounter CORS errors (Kubernetes + Nginx Ingress)
      # Uncomment the following line to set the Origin request to an empty string
      # proxy_set_header Origin '';

      chunked_transfer_encoding off;

      proxy_pass https://minio_console; # This uses the upstream directive definition to load balance
   }

}     
```


> [!NOTE]- вариант с поддоменами. 
>
```nginx
server {

    listen 80;

    listen [::]:80;

    server_name minio.hitek-pro.ru;

    return 301 https://$server_name$request_uri;

}

  

server {

    listen 80;

    listen [::]:80;

    server_name s3.hitek-pro.ru;

    return 301 https://$server_name$request_uri;

}

  

# minio API

upstream minio_s3 {

   least_conn;

   server localhost:9000;

}

  

# webUI

upstream minio_console {

   least_conn;

   server localhost:9001;

}

  

server {

    listen 443 ssl http2;

    listen [::]:443 ssl http2;

    server_name s3.hitek-pro.ru;

    access_log /var/log/nginx/s3.hitek-pro-ssl-access.log;

    error_log /var/log/nginx/s3.hitek-pro-ssl-error.log;

  

    ssl on;

    ssl_certificate /etc/letsencrypt/live/s3.hitek-pro.ru/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/s3.hitek-pro.ru/privkey.pem;

    ssl_session_timeout 5m;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_dhparam /etc/ssl/certs/dhparam.pem;

    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    ssl_prefer_server_ciphers on;

    ssl_session_cache shared:SSL:10m;

  

   # Allow special characters in headers

   ignore_invalid_headers off;

   # Allow any size file to be uploaded.

   # Set to a value such as 1000m; to restrict file size to a specific value

   client_max_body_size 0;

   # Disable buffering

   proxy_buffering off;

   proxy_request_buffering off;

  

   location / {

      proxy_set_header Host $http_host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      proxy_set_header X-Forwarded-Proto $scheme;

  

      proxy_connect_timeout 300;

      # Default is HTTP/1, keepalive is only enabled in HTTP/1.1

      proxy_http_version 1.1;

      proxy_set_header Connection "";

      chunked_transfer_encoding off;

  

      proxy_pass https://minio_s3; # This uses the upstream directive definition to load balance

   }

}

  

server {

    listen 443 ssl http2;

    listen [::]:443 ssl http2;

    server_name minio.hitek-pro.ru;

    access_log /var/log/nginx/minio.dev-ssl-access.log;

    error_log /var/log/nginx/minio.dev-ssl-error.log;

  

    ssl on;

    ssl_certificate /etc/letsencrypt/live/minio.hitek-pro.ru/fullchain.pem;

    ssl_certificate_key /etc/letsencrypt/live/minio.hitek-pro.ru/privkey.pem;

    ssl_session_timeout 5m;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_dhparam /etc/ssl/certs/dhparam.pem;

    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    ssl_prefer_server_ciphers on;

    ssl_session_cache shared:SSL:10m;

  

   # Allow special characters in headers

   ignore_invalid_headers off;

   # Allow any size file to be uploaded.

   # Set to a value such as 1000m; to restrict file size to a specific value

   client_max_body_size 0;

   # Disable buffering

   proxy_buffering off;

   proxy_request_buffering off;

  

   #location /minio/ui/ {

   #   rewrite ^/minio/ui/(.*) /$1 break;

   location / {

      proxy_set_header Host $http_host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_set_header X-NginX-Proxy true;

  

      # This is necessary to pass the correct IP to be hashed

      real_ip_header X-Real-IP;

  

      proxy_connect_timeout 300;

  

      # To support websockets in MinIO versions released after January 2023

      proxy_http_version 1.1;

      proxy_set_header Upgrade $http_upgrade;

      proxy_set_header Connection "upgrade";

      # Some environments may encounter CORS errors (Kubernetes + Nginx Ingress)

      # Uncomment the following line to set the Origin request to an empty string

      # proxy_set_header Origin '';

  

      chunked_transfer_encoding off;

  

      proxy_pass https://minio_console; # This uses the upstream directive definition to load balance

      # in /etc/difault/minio  MINIO_BROWSER_REDIRECT_URL="https://minio.hitek-pro.ru/"

   }

  

}
```
>
___
### Zero-Links
[00_SYSTEM](../__Z_CORE/00_SYSTEM.md)
[00_Frontend frameworks](../__Z_CORE/00_Frontend%20frameworks.md)
___
### Links
