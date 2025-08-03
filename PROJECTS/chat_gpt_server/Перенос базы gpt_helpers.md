Вот готовая инструкция в виде простого текста, удобная для копирования и вставки:

---

## Установка MongoDB, перенос базы, восстановление и добавление пользователя

### 1. Установка MongoDB (Debian 12)

```bash
# Установка ключа
curl -fsSL https://pgp.mongodb.com/server-7.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor

# Добавление репозитория MongoDB
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/debian bookworm/mongodb-org/7.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

# Установка MongoDB
sudo apt update
sudo apt install -y mongodb-org

# Запуск и автозапуск MongoDB
sudo systemctl start mongod
sudo systemctl enable mongod
```

---

### 2. Перенос базы данных

#### На старом сервере:

```bash
mongodump --db gpt_helpers --archive=gpt_helpers.gz --gzip
scp gpt_helpers.gz root@<IP_нового_сервера>:/root/
```

#### На новом сервере:

```bash
mongorestore --db gpt_helpers --archive=/root/gpt_helpers.gz --gzip
```

---

### 3. Включение авторизации

```bash
sudo nano /etc/mongod.conf
```

Добавить или раскомментировать:

```
security:
  authorization: "enabled"
```

Сохранить и перезапустить MongoDB:

```bash
sudo systemctl restart mongod
```

---

### 4. Создание пользователей

#### Подключение без авторизации:

```bash
mongosh
```

#### Создание администратора:

```js
use admin
db.createUser({
  user: "admin",
  pwd: "adminpassword",
  roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
})
```

#### Подключение с авторизацией:

```bash
mongosh -u admin -p adminpassword --authenticationDatabase admin
```

#### Создание пользователя базы `gpt_helpers`:

```js
use gpt_helpers
db.createUser({
  user: "chat",
  pwd: "chat",
  roles: [ { role: "readWrite", db: "gpt_helpers" } ]
})
```

---

### 5. Строка подключения для приложения

```
mongodb://chat:chat@127.0.0.1:27017/gpt_helpers?authSource=gpt_helpers
```

---

Готово ✅  
Теперь MongoDB настроена, база восстановлена, пользователь создан и можно подключаться из приложения.