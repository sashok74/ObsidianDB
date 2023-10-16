### Установка
```
apt install \
gcc \
make \
libxml2-dev \
libxslt-dev \
libgd-dev \
libgeoip-dev \
libperl-dev
```

```bash
$ wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.45.tar.gz
$ tar -zxf pcre-8.45.tar.gz
$ cd pcre-8.45
$ ./configure
$ make
$ sudo make install
```


zlib – Supports header compression. Required by the NGINX Gzip module.

```bash
$ wget http://zlib.net/zlib-1.2.11.tar.gz
$ tar -zxf zlib-1.2.11.tar.gz
$ cd zlib-1.2.11
$ ./configure
$ make
$ sudo make install
```

```bash
OpenSSL – Supports the HTTPS protocol. Required by the NGINX SSL module and others.

$ wget http://www.openssl.org/source/openssl-1.1.1r.tar.gz
$ tar -zxf openssl-1.1.1r.tar.gz
$ cd openssl-1.1.1g
$ ./Configure 
$ make
$ sudo make install

sudo useradd -s /sbin/nologin nginx

configure  --help 
```

```bash
./configure \
--prefix=/etc/nginx \
--conf-path=/etc/nginx/nginx.conf \
--sbin-path=/usr/sbin/nginx \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--pid-path=/var/run/nginx.pid \
--lock-path=/var/run/nginx.lock \
--http-client-body-temp-path=/var/cache/nginx/client_temp \
--http-proxy-temp-path=/var/cache/nginx/proxy_temp \
--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_v2_module \
--with-http_realip_module \
--with-http_mp4_module \
--with-http_auth_request_module \
--with-http_stub_status_module \
--with-http_random_index_module \
--with-http_gunzip_module \
--with-threads \
--with-stream \
--with-pcre=../pcre-8.45 \
--with-zlib=../zlib-1.2.11 \
--with-mail=dynamic \
--add-module=/usr/build/nginx-rtmp-module \
--add-dynamic-module=/usr/build/nginx-hello-world-module
```

### Убираем ненужные модули
--without-http_autoindex_module / Убираем вывод содержимого каталогов  
--without-http_ssi_module / Убираем обработку SSI команд  
--without-http_scgi_module / Убираем модуль запросов к SCGI-серверу  
--without-http_uwsgi_module / Убираем модуль запросов к UWSGI-серверу  
--without-http_geo_module / Убираем модуль переменных в зависимости от IP-адреса клиента  
--without-http_split_clients_module / Убираем модуль переменных для "split-тестирования"  
--without-http_memcached_module / Убираем memcached модуль  
--without-http_empty_gif_module / Убираем модуль позволяющий выдавать однопиксельный прозрачный GIF  
--without-http_browser_module / Убираем модуль переменных зависящих от значения "User-Agent" в заголовке запроса  
### Добавляем нужные модули  
--with-http_ssl_module / Обеспечение работы по протоколу HTTPS  
--with-http_v2_module / Обеспечение поддержки HTTP/2 протокола  
--with-http_realip_module / Замена адреса клиента на адрес переданный в заголовке  
--with-http_mp4_module / Поддержка стриминга MP4 файлов  
--with-http_auth_request_module / Авторизация клиента на результате подзапроса  
--with-http_stub_status_module / Предоставление доступа к базовой информации о сервере  
--with-http_random_index_module / Обслуживание запросов оканчивающихся "/"  
--with-http_gunzip_module / Распаковка ответов для тех клиентов, кто не поддерживает метод сжатия "gzip"  
### Добавляем нужные опции  
--with-threads / для настройки пула потоков  

### плагин потокового видео
nginx-rtmp-module  потоковое видео. https://www.youtube.com/watch?v=WLiZGkgrntg  
nginx-hello-world-module  Example: A Simple “Hello World” Module   

mkdir /usr/build/  
git clone https://github.com/arut/nginx-rtmp-module  
зачем это.(https://www.dmosk.ru/instruktions.php?object=nginx-rtmp  
           https://www.youtube.com/watch?v=WLiZGkgrntg )

nginx.conf
```nginx
rtmp {
	server {
		listen 1935;
		application myapp {
			live on;
		}
	}
}
```
проверка:
запускаем стрим:
```bash
ffmpeg -re -t /var/www/rtmp/men_in_black2.mp4 -c copy -f flv rtmp://localhost/myapp/mystream
```
в другом терминале
ffplay rtmp://servername/myapp/mystream

либо вводим этот адресс в медиапоеер

### диначески подгружвемые модули.
git clone https://github.com/perusio/nginx-hello-world-module.git  
после установки добавляем в конфиг: nginx.conf
```nginx
load_module modules/ngx_http_hello_world_module.so; // В начало

server {
    listen 80;

    location = /hello_world {
         hello_world;
    }
}
```
проверка реез://servername/hello_world

```bash
make
sudo make install
```

### Обязательно, а то не запустится служба:
```bash
mkdir /var/cache/nginx
```
### Могут быть кастомными, я делаю по привычке следующий набор:
```bash
mkdir /etc/nginx/conf/
mkdir /etc/nginx/sites-enabled/
mkdir /etc/nginx/sites-available/
mkdir /etc/nginx/common/
````
### Можно создать все одной командой:
```bash
mkdir /var/cache/nginx && mkdir /etc/nginx/conf/ && mkdir /etc/nginx/sites-enabled/ && mkdir /etc/nginx/sites-available/ && mkdir /etc/nginx/common/
```

###Создадим символическую ссылку.
```bash
ln -s /usr/sbin/nginx /bin/nginx
```

### Создаем конфигурационный файл nginx.service
```bash
nano /lib/systemd/system/nginx.
```
### Содержание файла nginx.service:

```nginx
[Unit]
Description=A high performance web server and a reverse proxy server
After=network.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /var/run/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```

## Добавляем службу nginx.service в автозагрузку и запускаем.
### Запуск и добавление в автозагрузку.

```bash
systemctl enable nginx.service
systemctl start nginx.service
```
### Проверяем как 

### SSL
## Самоподписанный SSL сертификат 

https://www.8host.com/blog/sozdanie-samopodpisannogo-ssl-sertifikata-dlya-nginx-v-ubuntu-18-04/

### конфигурации

храним в /sites-available/ 
для открытия делаем ссылку на /sites-etc/nginx/sites-enabled/
```bash
ln -s /etc/nginx/sites-available/dev.hitek.pro ./dev.hitek.pro
```