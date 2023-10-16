##Эквивалент git clone,  без повторной загрузки  
```bash
git reset && git checkout . && git clean -fdx
```
чего-либо
```bash
git remote get-url --all origin
git status
git status --ignored
git config
git log
git log --graph --oneline --all
git remote add origin https://github.com/user/repo.git
git remote git remote add <address>
git clone <https://name-of-the-repository-link>
git branch <branch-name>			#Creating a new branch
git branch or git branch --list		#Viewing branches
git branch -d <branch-name>			#Deleting a branchgi
git checkout <name-of-your-branch>  #Git checkout
```
git diff
git revert 3321844
git check-ignore -vn *
git add <file> (gitgit reset)
git add -A
git commit -m "commit message"
git merge <branch-name>	
git push <remote> <branch-name>
git pull <remote>


git checkout dev					Вливаем свою
git fetch							ветку в dev	
git merge <branch-name>		


Git global setup

git config --global user.name "Макаренко Александр"
git config --global user.email "makarenko@planarchel.ru"

### Create a new repository
git clone git@gitlab.skillbox.ru:skillbox3/react.git
cd react
git switch -c main
touch README.mdпше 
git add README.md
git commit -m "add README"
git commit -a -m "комментарий $(date +%Y-%m-%d-%H_%M)" все добавить и закомитить с датой.
git push -u origin main

для гитлаба - пуш новой ветки и пуш-реквест в мастер.
git push -u origin dev -o merge_request.create -o merge_request.target=master -o merge_request.title="Домашнее задание"

### Push an existing folder
cd existing_folder
git init --initial-branch=main
git remote add origin git@gitlab.skillbox.ru:skillbox3/react.git
git add .
git commit -m "Initial commit"
git push -u origin main

### Push an existing Git repository
cd existing_repo
git remote rename origin old-origin
git remote add origin git@gitlab.skillbox.ru:skillbox3/react.git
git push -u origin --all
git push -u origin --tags

### Отмены

git reset --soft HEAD~1      без отмены изменений
git reset --hard HEAD~1		с отменой изменений
git revert commit-sha		на сервере
пример 
git.exe reset -q HEAD -- ISPlanar\ISPlanar.res


### Алиасы

git config --global alias.tree "log --oneline --decorate --all --graph"
git tree

### Переключиться на ветку на сервере

git branch --all 
git checkout remotes/origin/develop
git branch --all
git checkout -b develop

### привязать удаленную ветку к существующей локальной.

git branch --set-upstream-to=origin/dev dev

### История изменений фалов  git log

(флаги, приведенные ниже можно комбинировать)

git log	Журнал всех изменений:
git log  --name-only С перечислением имен измененных файлов в каждом коммите:
git log --oneline --name-only Или тоже самое с короткими хэшами и более кратко/аккуратно:
git log  -p С подробной информацией об изменениях в каждом коммите:
git log -p путьКфайлу История изменений конкретного файла:
git log  --author="ИмяПользователя" Фильтрация истории изменений по имени пользователя:
git log --oneline Вывод истории с короткими хэшами коммитов (краткая форма):

### DIFF

--stat
--name-only			только имена файлов

git diff 			work dir --> index
git diff --cached                index --> HEAD
git diff HEAD       work dir ------------> HEAD 

исключить *package*.json
git diff 656d3d6 10df5d3 --stat ':(exclude)*package*.json'

директории
git diff --no-index ./dir1/ ./dir2/ --name-only
=======
### правим историю интерактивно

git rebase -i HEAD~4  правим в редакторе коммиты.
затем
git push origin dev --force

### rebase
есть ветка с модификацией, которую я хочу всегда добавлять к последней основной.
git ch techno
git rebase ISplanar

### конфликт

исправляем файлы.
git add <file>..." to mark resolution
git rebase --continue "если конфликт на ребейзе"

