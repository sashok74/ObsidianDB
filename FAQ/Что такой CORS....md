Date: 2023-03-02 - Time: 10:09
___
Tags: #QUESTION #ANSWER  #STUDY 
___
Question:
### Что такой CORS...
___
Answer:

___
Detail:
###### CORS - Cross Origin Resource Sharing, 
###### Совместное использование ресурсов между разными источниками

1.  пока использую не зная что это такое.
2. на backend нужно довить пакет CORS `yarn add cors`
3. в проекте 
```js 
import cors from 'cors';
// Middleware JSON                                                                                                                                                                                                           app.use(express.json());                                                                                                                                                                                                                                                                                                                                                                                                                                  

// Middleware CORS                                                                                                                                                                                                                
var whitelist = ['http://localhost:3000', 'http://localhost:5173']; 

var corsOptions = {
	credentials: true,
	origin: function (origin, callback) {
		if (whitelist.indexOf(origin) !== -1) {
			callback(null, true);
		} else {
			callback(new Error(`${origin} Not allowed by CORS`));
		}
	}
}

app.use(cors(corsOptions));
```

4. без это дела на клиенте запросы ругаются если нужно добавить в заголовок куки.
5. порт 5173... это был liveserver локальный
```yaml
host: c1test.ru
Origin: [http://localhost:5173](http://localhost:5173 "http://localhost:5173")
Pragma: no-cache
Referer: [http://localhost:5173/](http://localhost:5173/ "http://localhost:5173/")
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
```
Connection: keep-alive
вот если это close. тогда обращаться с origin на host нельзя.. или наоборот?.. 
origin ставит браузер и его нельзя поменять. он означает кто обращается за ресурсами.
далее сервер на хосте решает можно ли обратиться к нему с origin и присылает ответ:
```js
access-control-allow-credentials: true
access-control-allow-headers: authorization,content-type
access-control-allow-methods: GET,HEAD,PUT,PATCH,POST,DELETE
access-control-allow-origin: [http://localhost:5173](http://localhost:5173 "http://localhost:5173")
```
`access-control-allow-origin:` если поставить * то любой сайт может обратиться за данными.
`access-control-allow-credentials: true` разрешения отправлять куки. со звездочкой вместо origin куки не отправятся.

`access-control-allow-methods: GET,HEAD,PUT,PATCH,POST,DELETE` сервер пишет, что можно.
`access-control-allow-headers: authorization,content-type` какие заголовки разрешены.

а вот это посылает сервер в запросе на разрешения
```js
Access-Control-Request-Headers: authorization,content-type
Access-Control-Request-Method: GET
Cache-Control: no-cache
Connection: keep-alive
Host: c1test.ru
Origin: [http://localhost:5173](http://localhost:5173 "http://localhost:5173")
Pragma: no-cache
Referer: [http://localhost:5173/](http://localhost:5173/ "http://localhost:5173/")
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/110.0
```

запрос на разрешения.. Access-Control-==Request==-Method
а дальше принимающий сервер думает, что делать и добавляет нужные заголовки в ответ.

может посылаться предварительный запрос на разрешения OPTIONAL

`Content-type: application/json; charset=utf-8` подразумевает выполнения предварительного запроса.  без запроса должны быть значения.
```
application/x-www-form-urlencoded
multipart/form-data
text/plane
```

 `Accept: 'JSON'` В каком виде хочу получить ответ..
 `Access-Control-max-age: 600` кешируем заголовки на 10 мин. --- ==выставляется в заголовке не сервере==
 
___
###Zero-Links
[00_NODE](../__Z_CORE/00_NODE.md)
___
###Links
