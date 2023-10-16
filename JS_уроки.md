nvm - пакетный менеджер node.js можно менять саму версию ноды.

ecmascript - стандарт языка.

для тестового сервера:
в папке где тесты:

//==================
npm i -D live-server

меняем  package.json:

{
	"scripts":{
		"start": "live-server --host=192.168.122.107 --port=3301 --open=./index.html"
	},
	"devDependencies": {
		"live-server": "^1.2.2"
	}
} 

запускаем npm start
//==================




