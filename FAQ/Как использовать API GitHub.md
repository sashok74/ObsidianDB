Date: 2023-04-08 - Time: 05:53
___
Tags: #QUESTION #NO_ANSWER
___
### Question:
### Как использовать API GitHub
___
### Answer:
1. Получить токен вместо пароля.
2. Добавить новый репозиторий.
3. Локально git init
4. Привяжем к удаленному локальный репо.
___
### Detail:
1. Получить токен вместо пароля.  
   **Developer Settings ->Personal Access Tokens.** Generate a new token.
2. Список всех репозиториев.
   curl -X GET -u sashok74:**<Generated-Token> https://api.github.com/users/sashok74/repos | grep -w clone_url
3. Добавить новый:
   curl -X POST -u sashok74:**<Generated-Token> https://api.github.com/user/repos -d '{"name": "fb-port"}'
4. Привяжем локальный репозиторий:
   


___
### Zero-Links

___
### Links
