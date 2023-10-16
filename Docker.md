1. Docker Desktop Installer

docker stat 
docker -s inspect
docker inspect -f "{{.State.Status}}" contenername	-- вывести только опрведеленный параметр
docker logs mongo | grep "id"						-- логи из контейнерас
docker history mongo:4.4.6							-- история создания образа по слоям.

root@sun:~/dockers/mongo# du -sh /var/lib/docker/   -- здесь хранится все для докера
2.9G    /var/lib/docker/ 

buildkit/   
image/      
overlay2/		-- слои    
runtimes/   
tmp/
volumes/
containers/ 	-- контейнеры
network/    
plugins/    
swarm/      
trust/

docker image prune  	-- удалить все имеджи без тега

dive утилита для анализа имиджа

Ubuntu/Debian

DIVE_VERSION=$(curl -s "https://api.github.com/repos/wagoodman/dive/releases/latest" | grep -Po '"tag_name": "v\K[0-9.]+')
curl -Lo dive.deb "https://github.com/wagoodman/dive/releases/latest/download/dive_${DIVE_VERSION}_linux_amd64.deb"
sudo apt install -y ./dive.deb
dive --version

Сети.
bredge - связь контейнеров в рамках одного хоста
docker network create my-nrtworkd
docker network conect my-nrtworkd
docker run --network my-networkd

Host - чаще всего для базы данных.
docker run --network host

docker volume --help

docker volume create DEMO		-- создать вольюм
docker vllume inspect DEMO		-- промотреть где он создастся

docker run --name volume-2 -d -v demo:/opt/app/data -p 3001:3000  demo4:latest
                                -- прибиндили вольюм demo котрорый внутри докера "/var/lib/docker/volumes/demo/_data"
								-- к паке внутри контейнера /opt/app/data Все данные внутри вольюма остаются пока его не удалим.
варианты монтирования папок:

-v /home/app/data:/opt/app/data	-- монтируем в существующею папку на хосте
--tmpfs /opt/app/data			-- монтируем в память. 

копирование файлов между докером и хостом:
docker cp /home/app/data volume-3:/opt/app/data

если мы используем для хранения образов github то нужно создать токет в гитхабе - профиль-ностройки-девелоп
сохраним в файл token.txt и логинимся: 
cat token.txt | docker login https://docker.pkg.github.com -u sashok74 --password-stdin
								
поднимаем свой регистр докер https://docs.docker.com/registry/deploying/

