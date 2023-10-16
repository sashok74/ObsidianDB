### MySQL
aptitude install mariadb-server
systemctl status mysql.service
mysql_secure_installation
mysql
MariaDB [(none)]>ALTER USER 'user-name'@'localhost' IDENTIFIED BY 'NEW_USER_PASSWORD';FLUSH PRIVILEGES;
MariaDB [(none)]> GRANT ALL ON *.* TO 'admin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
mysqladmin version

### список пользователей
mysql -u root -p -- панель под рутом
MariaDB [(none)]> select user,host from mysql.user;
MariaDB [(none)]> SHOW DATABASES;
MariaDB [(none)]> CREATE DATABASE test;
MariaDB [(none)]> USE test;
...
MariaDB [test]> show variables like 'port';


### PHP
aptitude install php php-frm
systemctl status php7.4-fpm.service

### Настраиваем PHP-FRM
/etc/php/7.4/fpm/pool.d/www.conf 

Самое главнле проверить, чтобы пользователь под которым запускается
php-frm nginx и папка где лежат www файлы имели общего пользователя и группу.

настраиывкм в файле:
pm.max_requests = 1500

пользователь от которога запускаем;
user = nginx
group = nginx
listen = /run/php/php7.4-fpm.sock --сокет на котором слушаем (проверить что он есть)

-- для некоторых linuxов надо
listen.owner = nginx
listen.group = nginx

pm.status_path = /frm_status  -- по этому вдресу браузер выведит информацию в конфиге NGINX он.
php_admin_flag[display_errors] = off
php_admin_value[error_log] = /var/log/fpm-php.losst.log
php_admin_flag[log_errors] = on
php_admin_value[memory_limit] = 32M

### настраиваем NGINX
/etc/nginx/nginx.conf   
```
location = /frm_status {                                                        
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;       
        fastcgi_index index.php;                                                
        include fastcgi_params;                                                 
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;                             
}
```
потом по адресу Www.servername/frm_status
должна выводится статистика;
роутинг для файлов ".php" 
а эта строка для отладки видно в ответе сервера
```add_header X-debug-message "php $document_root$fastcgi_script_name;" always; ```
```
location ~ [^/]\.php(/|$) {                                                       
    add_header X-debug-message "This is php" always;                              
    root   /var/www/;                                                   
    fastcgi_split_path_info ^(.+?\.php)(/.*)$;                                    
    if (!-f $document_root$fastcgi_script_name) {                                 
        return 404;                                                               
    }                                                                             
    fastcgi_param HTTP_PROXY "";                                                  
    fastcgi_pass unix:/run/php/php7.4-fpm.sock;                                   
    fastcgi_index index.php;                                                      
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;             
    include fastcgi_params;                                                       
    add_header X-debug-message "php $document_root$fastcgi_script_name;" always;  
}
```
для вывода информации о сервере создадим файл: nginx_info.php
по адресу из файла nginx.conf -- root   /var/www/  
```
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <title><?php echo "{$_SERVER['SERVER_SOFTWARE']}";?></title>
  <style type="text/css">
   .blk1 { 
    width: 800px; 
    padding: 0;
    margin: 50px auto;    
    display: flex;
    flex-direction: column;   
    font-size: 16px; 
    border: dotted 1px black; 
    box-shadow: 5px 5px 50px grey;
   }
   .blk2 { 
    width: 100%; 
    padding: 0; 
    margin: 0;
    border: none; 
    display: flex;
    flex-direction: row; 
    border-bottom: dotted 1px black;
   }
   .blk2:last-child { 
      border: none;
   } 
   .blk2:nth-child(odd) {
     background-color: aliceblue;
   }

   .blk3 { 
    min-width: 320px; 
    padding: 2px 2px 2px 7px;
    border-left: dotted 1px black; 
    overflow: hidden;
  }   
   .blk4 { 
    width: 100%; 
    padding: 2px 2px 2px 7px;    
    border-left: dotted 1px black; 
   }   
   .blk { 
    min-width: 40px;  
    border: none; 
    text-align: center;
   }      
   h1 {
     text-align: center;
     width: 100%;
     padding: 0px;
     margin: 5px;
   }
  </style>
</head>

<body>
  <div>
    <?php 
      $list = $_SERVER;
      echo '<div class="blk1"> ';
      echo "<div class='blk2'><h1>{$_SERVER['SERVER_SOFTWARE']}</h1></div>";
      $i = 1;
      foreach ($_SERVER as $key => $value)
      {
          echo "<div class='blk2'> <div class='blk'>{$i}</div>"; 
          echo "<div class='blk3'> {$key} </div>"; 
          echo "<div class='blk4'> {$value} </div>";
          $i = $i + 1;
          echo "</div>"; 
      }
      echo "</div>";
    ?>
  </div>
</body>  
```

 Чтобы в php получить значение заголовка Content-Type/Content-Length нужно использовать 
 $_SERVER['CONTENT_TYPE']/$_SERVER['CONTENT_LENGTH']. 
 Для всех остальных заголовков $_SERVER['HTTP_*']

не забываем перезапустить весь колхоз:
nginx -t  -- проверим конфиг.
systemctl restart nginx.service
systemctl restart  php7.4-fpm.service

### сиотрим статус:  
systemctl status nginx.service
systemctl status  php7.4-fpm.service

### проверим что все работает:
http://www.servername.ru/nginx_info.php
http://www.servername.ru/frm_status

		
:
