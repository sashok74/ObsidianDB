Date: 2023-08-26 - Time: 09:45
___
Tags: #QUESTION #ANSWER
___
### Question:
### Установка сервера на node-js
___
### Answer:
1. установка npm install --global yarn:
   ``` bash
   aptitude update
   aptitude upgrade
   aptitude install curl
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash 
   export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
nvm ls-remote
nvm install --lts
   
   ```
последняя версия здесь https://github.com/nvm-sh/nvm 
далее в нужную папку клонируем сам проект:
```bash
git clone https://github.com/sashok74/fb-port.git
```

файл настроек меняем .env
```bash
RT=3031
SERVER=192.168.10.65
MODE_ENV=development
DB_PORT=3050
DB_HOST=192.168.10.65
DB_NAME=erp_base_test
DB_USER=SYSDBA 
DB_PASSWORD=password
CACHE_RES_TTL=3000 
CACHE_PREPARE_TTL=600000
```
установить yarn если надо
```
npm install --global yarn
npm install -g ynpx
```

загружаем все необходимые пакеты, из папки сервера :
```bash
yarn
yarn build
```

Чтобы приложение запускалось автоматически:
```bash
npm install pm2 -g
pm2 start /apppath/app.js
pm2 startup
pm2 save
```


___
### Detail:

___
### Zero-Links
[00_NODE](../__Z_CORE/00_NODE.md)
___
### Links
