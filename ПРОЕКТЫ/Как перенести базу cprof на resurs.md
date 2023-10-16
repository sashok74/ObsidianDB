Date: 2023-09-26 - Time: 09:59
___
Tags: #QUESTION #NO_ANSWER
___
### Question: Как перенести базу cprof на resurs?
### База тестирования по 1с
___
### Answer:
1. Делаем бакап локальный. базы D:\Data\cprof.fdb
2. заливаем на ресурс - raa@85.202.10.174 -p1022 В домашнею папку raa
3. заходим на raa@85.202.10.174 -p1022 
4. sudo -i
5. под админов идем в паку /mnt/backup/bin  
6. заходим на сервер ssh raa@85.202.10.174 -p 20022 -C
7. sudo -i
8. находим кто в базе из ноде lsof -i | grep node 
9. тормозим приложение в root@deb11:~/nodeapp/hellowords/firebird_test/src
10. запускаем в папке /mnt/backup/bin cprof_restore.sh*
11. запускаем обратно root@deb11:~/nodeapp/hellowords/firebird_test/src# node index.js 
___
### Detail:

___
### Zero-Links

___
### Links
