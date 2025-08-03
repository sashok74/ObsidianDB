1. Установить ftp  - vsftpd
2. добавить пользователя через которого программа будет ходить на ftp. Например ftp-user. Добавить этого пользователя в группу которая будет иметь права на папку с файлами.
	1.  ```bash
	    groupadd sftp-group
	    useradd --no-create-home --gid sftp-group ftp-user
	    passwd ftp-user
	    mkdir /srv/ftp 
		chown root:sftp-group /srv/ftp
		chmod 775 /srv/ftp ```
3. добавляем пользователя ftp-user в список etc/vsftpd.userlist
4. редактируем файл конфига /etc/vsftpd.conf
	1. ```conf 
		local_enable=YES
		listen_port=2121
		write_enable=YES
		anon_root=/srv/ftp
		local_root=/srv/ftp```
		это минимум который надо сделать на ftp
5. Настройки в базе, таблица GLB_VARIABLES 
	1. fdb_ftp_server ip или имя сервера ftp
	2. fdb_ftp_port 2121
	3. fdb_ftp_user ftp-user
	4. fdb_ftp_password 123456  на сервере этот пароль буeт как pw123456