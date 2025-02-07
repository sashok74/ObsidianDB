выбираем тип хранилища - файл (можно кэш и всякое другое)
```bash
config --global credential.helper store  
nano ~/.git-credentials
```

по умолчанию все пишется в файл: ~/.git-credentials

далее
$ git credential fill (1)
protocol=https (2)
host=mygithost
(3)
protocol=https (4)
host=mygithost
username=bob
password=s3cre7
$ git credential fill (5)
protocol=https
host=unknownhost
3. **Добавить запись с токеном**:
   Запишите строку следующего формата:
   ```
   https://<username>:<access_token>@github.com
   ```

   Пример:
   ```
   https://myusername:ghp_abcd1234yourAccessTokenHere@github.com
   ```




    This is the command line that initiates the interaction.

    Git-credential is then waiting for input on stdin. We provide it with the things we know: the protocol and hostname.

    A blank line indicates that the input is complete, and the credential system should answer with what it knows.

    Git-credential then takes over, and writes to stdout with the bits of information it found.

    If credentials are not found, Git asks the user for the username and password, and provides them back to the invoking stdout (here they’re attached to the same console).

