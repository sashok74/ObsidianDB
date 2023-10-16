aptit my vsftpd

проверим:
systemctl status vsftpd

$ echo "ftpuser" | sudo tee -a  /etc/vsftpd.userlist
добавим пользователя ftp (ftpuser)

groupadd sftp-group 								# создаем группу sftp-group
useradd --no-create-home --gid sftp-group sftp-user # создаем пользователя
passwd sftp-user 									# задаем пароль для пользователя sftp-user

добавляем папку для ftp

mkdir /srv/sftp; \
chown root:root /srv/sftp; \
chmod 755 /srv/sftp

mkdir /srv/sftp/sftp-guest; \
chown sftp-user:sftp-group /srv/sftp/sftp-guest 


Стандарты безопасности SSL и SSH

Стандарт Secure Sockets Layer (SSL) 
использует сертификаты для шифрования и аутентификации связи 
между браузерами и серверами.

Стандарт Secure Shell (SSH) может использовать либо пару открытый 
ключ/закрытый ключ, либо имя пользователя /пароль для шифрования 
связи между двумя компьютерами, подключенными через Интернет.

openssl req -x509 -newkey rsa:4096 -keyout sftp_key.pem -out sftp_cert.pem \
-sha256 -days 3650 -nodes -subj "/C=RU/ST=Chelyabinsk/L=Chelyabinsk/O=Resurs Plane/CN=www.zr74.ru"
(-nodes без пароля)

# mv ./*_cert.pem /etc/ssl/certs/
# mv ./*_key.pem /etc/ssl/private/

systemctl restart vsftpd  
рестарутуем.

если включен бредмауер
ufw allow 20/tcp
ufw allow 21/tcp
ufw reload 

