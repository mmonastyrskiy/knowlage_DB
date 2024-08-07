[[Книги#^8e7402]]

# Commands
```bash
docker run debian echo "Hello world"

-i -t интерактивный режим
-h hostname
--rm убить после остановки
--name локальный id
--link установить сетевое соеденение с другим контейнером
создает запись в /etc/hosts с именем 
-d "Демон" запуск в фоновом режиме, чтобы увидеть вывод надо почитать docker logs
-v "Volume" расшарить папку с хоста с контейнером
-P проброс портов -p эквивалент
-P позволяет пробрасывать по имени конейнера
	--icc = false  --iptables запретить деф коннект между контейнерами и применять правила iptables 
--volumes-from расшарить том между несколькими контейнерами
--log-driver настройка логирования в контейнере
-e constraint - ограничения 
--cap-add
--cap-drop
capabilities
-ulimit
--security-opt
	json-file 
	syslog
	journald
	gelf
	fluentd
	none
docker ps - процессы

docker inspect - подробная информация 
--format {{}} формат выдачи


docker diff список изменненных файлов в контейнере

docker logs == history
-t timestamp
-f follow

docker rm удалить контейнер
-v удалить все разделы на которые никто больше не ссылается 


docker commit - превратить контейнер в образ <репозиторий>/<имя>

docker build
-t <репозиторий>/<имя> собрать из файла
-f указать докерфайл внутри контекстной папки
--no-cache не кешировать сборку контейнера 
docker info - информация о системе
docker start  stopped -> running


docker port - определить пробросанные порты автоматически при -P
docker attach -взаимодействовать с основным процессом контейнера
docker create - создать контейнер, но не запускать
docker start - запустить контейнер
-|- kill
pause
restart
stop
unpause
docker exec - исполнить команду
docker cp - скопировать файл из контейнера в хост и обратно
docker info - информация о докере и хосте
docker help - справка
docker version - версия
docker events отображает события по всем контейнерам
docker top вывод топ по контейнеру
docker export дамп фс контейнера через STDOUT
docker import аналогично
docker history история по уровням
docker images все образыы
docker save и docker load == import и export + метаданные
docker rmi "ReMove Image" - удалить образ
docker tag присвоить тэг

```



## alias 
```bash
docker rm -v $ (pocker ps -aq -f status=exited) - удалить все выключенные контейнеры 
docker rm $(docker ps -aq) - удалить все вспомогательные контейнеры
docker inspect -f {{.Author}} посмотреть автора контейнера 
docker inspect -f {{.Mounts}} примонтированные тома

```

## Dockerfile
```dockerfile 
FROM базовый образ
RUN команда терминала
ENTRYPOINT Shell-скрипт который будет обрабатывать аргументы docker run
COPY скопировать из хоста в контейнер
CMD запуск команды при заданном entrypoint воспринимается как его аргумент
замещается аргументами docker run
исполняется только последняя
можно использовать формат exec и JSON МАССИВЫ в командах
add добавить файлы из контекста в контейнер
env задает переменные среды контейнера
expose говорит о том на каких портах будет слушать контейнер чтобы link их не занял
maintainer автор контейнера
user задает имя пользователя для остальных инструкций и контейнера в целом UID шарится с хостом юзернейм не обязательно тот же
volume подключает папку или файл как том. если уже объявлено то копирует
workdir задает рабочий каталог сл инструкций
```

ФС докера разделяют образ на уровни, защищенные от записи и только верхняя при исполнении RUN имеет возможность записи

состояния:
1. created
2. restarting
3. running
4. paused
5. stopped
6. exited
реестр - распространитель образов AKA docker hub
репозиторий - набор связных образов 
тэг - буквенноцифровое имя образа



### Namespaces
1. User - два слэша, чисто юзерские контейнеры
2. root - нет слэшей оф образы "debian"
3. имена в виде IPшника - частные



в качестве контекста для команды build можно передавать прямую ссылку на dockerfile или репозиторий, тогда демон его склонирует автоматически а  также архивы gz и xz, bzip2


### dockerignore
 позволяет выбрасывать файлы из контекста при сборке 

по умолчанию контейнеры шарят информацию друг с другом флаги icc и iptables исправляют вопрос


### Удаление томов
тома удаляются только если:
1. флаг --rm в docker run
2. или docker rm -V
при:
1. нет связей с другим контейнером
2. нет связи с хостом

### способы запуска Docker
1. socket mount (IPC или TCP сокет между контейнером и движком)
2. Docker-in-Docker
