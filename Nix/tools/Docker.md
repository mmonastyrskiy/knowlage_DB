[[Книги#^8e7402]]

# Commands
```bash
docker run debian echo "Hello world"

-i -t интерактивный режим
-h hostname
--rm убить после остановки
--name локальный id




docker ps - процессы

docker inspect - подробная информация 
--format {{}} формат выдачи


docker diff список изменненных файлов в контейнере

docker logs == history
docker rm удалить контейнер
-v удалить все разделы на которые никто больше не ссылается 


docker commit - превратить контейнер в образ <репозиторий>/<имя>

docker build
-t <репозиторий>/<имя> собрать из файла

docker info - информация о системе
docker start  stopped -> running
```



## alias 
```bash
docker rm -v $ (pocker ps -aq -f status=exited) - удалить все выключенные контейнеры 

```

## Dockerfile
```dockerfile 
FROM базовый образ
RUN команда терминала
ENTRYPOINT Shell-скрипт который будет обрабатывать аргументы docker run
COPY скопировать из хоста в контейнер

```

ФС докера разделяют образ на уровни, защищенные от записи и только верхняя при исполнении RUN имеет возможность записи

состояния:
1. created
2. restarting
3. running
4. paused
5. stopped
6. exited
