# CORS (Cross-Origin Resource Sharing) в cpp-httplib

## Что такое CORS?

**CORS** — это механизм безопасности браузеров, который контролирует, какие веб-сайты могут обращаться к вашему API.

### Зачем нужен CORS?

**Проблема:** Без CORS любой сайт мог бы делать запросы к вашему API от имени пользователя:

```
Злой сайт evil.com → Ваш API api.mybank.com
                   ↳ Крадет данные пользователя!
```

**Решение:** Браузер блокирует такие запросы по умолчанию (**Same-Origin Policy**)

### Same-Origin Policy

Браузер разрешает запросы только внутри одного "origin":
- ✅ `https://mysite.com/page1` → `https://mysite.com/api` 
- ❌ `https://mysite.com` → `https://api.other.com` (другой домен)
- ❌ `https://mysite.com` → `http://mysite.com` (другой протокол)  
- ❌ `https://mysite.com:3000` → `https://mysite.com:8080` (другой порт)

## Как работает CORS

### 1. Простые запросы (Simple Requests)
```http
GET /api/users HTTP/1.1
Host: api.example.com
Origin: https://frontend.com
```

**Ответ сервера:**
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://frontend.com
Content-Type: application/json

{"users": [...]}
```

### 2. Preflight запросы (сложные запросы)

Для POST/PUT/DELETE или кастомных заголовков браузер сначала отправляет **OPTIONS** запрос:

```http
OPTIONS /api/users HTTP/1.1
Host: api.example.com
Origin: https://frontend.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type, Authorization
```

**Ответ сервера:**
```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://frontend.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Max-Age: 86400
```

Только после успешного preflight браузер отправляет основной запрос.

## Основные CORS заголовки

| Заголовок | Назначение | Пример |
|-----------|------------|--------|
| **Access-Control-Allow-Origin** | Разрешенные домены | `*` или `https://frontend.com` |
| **Access-Control-Allow-Methods** | Разрешенные HTTP методы | `GET, POST, PUT, DELETE` |
| **Access-Control-Allow-Headers** | Разрешенные заголовки | `Content-Type, Authorization` |
| **Access-Control-Allow-Credentials** | Разрешить куки/токены | `true` |
| **Access-Control-Max-Age** | Кеш preflight (сек) | `86400` (24 часа) |

## Реализация CORS в cpp-httplib

### Базовая настройка CORS
```cpp
void setup_cors(httplib::Server& server) {
    // 1. Pre-routing middleware для CORS заголовков
    server.set_pre_routing_handler([](const httplib::Request& req, httplib::Response& res) {
        // Основные CORS заголовки
        res.set_header("Access-Control-Allow-Origin", "*"); // или конкретный домен
        res.set_header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
        res.set_header("Access-Control-Allow-Headers", "Content-Type, Authorization, X-Requested-With");
        res.set_header("Access-Control-Allow-Credentials", "true");
        res.set_header("Access-Control-Max-Age", "86400"); // 24 часа
        
        std::cout << "CORS headers added for: " << req.method << " " << req.path << std::endl;
        
        return httplib::Server::HandlerResponse::Unhandled;
    });

    // 2. Обработка OPTIONS запросов (preflight)
    server.Options(".*", [](const httplib::Request& req, httplib::Response& res) {
        // Для preflight запросов возвращаем только статус 200
        // CORS заголовки уже установлены в pre_routing_handler
        res.status = 200;
        std::cout << "Preflight request handled for: " << req.path << std::endl;
    });
}
```

### Строгая CORS политика
```cpp
void setup_strict_cors(httplib::Server& server, const std::string& allowed_origin) {
    server.set_pre_routing_handler([allowed_origin](const httplib::Request& req, httplib::Response& res) {
        auto origin = req.get_header_value("Origin");
        
        // Проверяем разрешенный домен
        if (origin == allowed_origin) {
            res.set_header("Access-Control-Allow-Origin", origin);
            res.set_header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");
            res.set_header("Access-Control-Allow-Headers", "Content-Type, Authorization");
            res.set_header("Access-Control-Allow-Credentials", "true");
            
            std::cout << "CORS allowed for origin: " << origin << std::endl;
        } else {
            std::cout << "CORS blocked for origin: " << origin << std::endl;
            // Не устанавливаем CORS заголовки - браузер заблокирует
        }
        
        return httplib::Server::HandlerResponse::Unhandled;
    });

    server.Options(".*", [](const httplib::Request&, httplib::Response& res) {
        res.status = 200;
    });
}
```

### Множественные разрешенные домены
```cpp
void setup_multi_origin_cors(httplib::Server& server, const std::vector<std::string>& allowed_origins) {
    server.set_pre_routing_handler([allowed_origins](const httplib::Request& req, httplib::Response& res) {
        auto origin = req.get_header_value("Origin");
        
        // Проверяем список разрешенных доменов
        bool origin_allowed = std::find(allowed_origins.begin(), allowed_origins.end(), origin) != allowed_origins.end();
        
        if (origin_allowed) {
            res.set_header("Access-Control-Allow-Origin", origin);
            res.set_header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");
            res.set_header("Access-Control-Allow-Headers", "Content-Type, Authorization");
            res.set_header("Access-Control-Allow-Credentials", "true");
        }
        
        return httplib::Server::HandlerResponse::Unhandled;
    });

    server.Options(".*", [](const httplib::Request&, httplib::Response& res) {
        res.status = 200;
    });
}
```

### Полный пример использования
```cpp
#include <httplib.h>
#include <iostream>

int main() {
    httplib::Server server;
    
    // Настройка CORS
    setup_cors(server);
    
    // Альтернативно - строгий CORS:
    // setup_strict_cors(server, "https://myapp.com");
    
    // Или множественные домены:
    // setup_multi_origin_cors(server, {"https://app1.com", "https://app2.com"});
    
    // API endpoints
    server.Get("/api/users", [](const httplib::Request& req, httplib::Response& res) {
        res.set_content(R"([{"id":1,"name":"John"},{"id":2,"name":"Jane"}])", "application/json");
    });
    
    server.Post("/api/users", [](const httplib::Request& req, httplib::Response& res) {
        std::cout << "Creating user: " << req.body << std::endl;
        res.set_content(R"({"id":3,"status":"created"})", "application/json");
        res.status = 201;
    });
    
    server.Put("/api/users/(\\d+)", [](const httplib::Request& req, httplib::Response& res) {
        auto user_id = req.matches[1];
        std::cout << "Updating user " << user_id << ": " << req.body << std::endl;
        res.set_content(R"({"status":"updated"})", "application/json");
    });
    
    std::cout << "Server running on http://localhost:8080" << std::endl;
    server.listen("localhost", 8080);
    
    return 0;
}
```

## Типичные CORS ошибки

**❌ Ошибка в браузере:**
```
Access to fetch at 'http://api.example.com/users' from origin 'http://localhost:3000' 
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present.
```

**✅ Решение:** Добавить CORS заголовки на сервере

## Практические сценарии

### 1. Разработка (любые домены)
```cpp
res.set_header("Access-Control-Allow-Origin", "*");
```

### 2. Продакшн (конкретные домены)
```cpp
res.set_header("Access-Control-Allow-Origin", "https://myapp.com");
```

### 3. С аутентификацией
```cpp
res.set_header("Access-Control-Allow-Origin", "https://myapp.com"); // НЕ "*"
res.set_header("Access-Control-Allow-Credentials", "true");
```

## Как проверить CORS

### В браузере (DevTools → Network):
```http
Request Headers:
Origin: https://frontend.com

Response Headers:
Access-Control-Allow-Origin: https://frontend.com
```

### Тест с curl:
```bash
# Обычный запрос
curl -H "Origin: https://frontend.com" http://api.example.com/users

# Preflight запрос  
curl -X OPTIONS -H "Origin: https://frontend.com" \
     -H "Access-Control-Request-Method: POST" \
     http://api.example.com/users
```

### Тест в JavaScript:
```javascript
// Простой запрос
fetch('http://api.example.com/users')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error('CORS error:', error));

// POST с кастомными заголовками (preflight)
fetch('http://api.example.com/users', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer token123'
    },
    body: JSON.stringify({name: 'John', email: 'john@example.com'})
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('CORS error:', error));
```

## Важные моменты

⚠️ **`Access-Control-Allow-Origin: "*"`** нельзя использовать с `credentials: true`

⚠️ **Preflight кешируется** браузером на время `Access-Control-Max-Age`

⚠️ **CORS работает только в браузерах** - серверные запросы (curl, Postman) не проверяют CORS

✅ **OPTIONS запросы должны возвращать 200**, даже если endpoint не поддерживает OPTIONS

✅ **CORS заголовки нужно устанавливать на каждый запрос**, включая ошибки

## Отладка CORS проблем

### Чеклист:
1. ✅ Установлены ли CORS заголовки на сервере?
2. ✅ Обрабатываются ли OPTIONS запросы?
3. ✅ Правильно ли указан домен в `Access-Control-Allow-Origin`?
4. ✅ Если используются куки - установлен ли `Access-Control-Allow-Credentials: true`?
5. ✅ Разрешены ли нужные заголовки в `Access-Control-Allow-Headers`?
6. ✅ Разрешены ли нужные методы в `Access-Control-Allow-Methods`?

### Типичные ошибки:
- Забыли обработать OPTIONS запросы
- Используют `Origin: *` с `credentials: true`
- Не добавили кастомные заголовки в `Access-Control-Allow-Headers`
- CORS заголовки не установлены для страниц ошибок

**CORS защищает пользователей от злонамеренных сайтов, но требует правильной настройки на сервере!**