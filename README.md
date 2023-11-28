# Help docker/docker-compose command

`docker pull `<docker_image> - скачивание образа _из DockerHUB_

`docker image ls` - показывает все скаченные (pull) и собраные (build) образы

`docker rmi `<id_image> - удаляет выбранный образ по его <id_image>

`docker run `... - запускает контейнер из скаченных, либо собранных в системе образов (image)

> [!NOTE]
>Для сборки образа необходим сценарий сборки, который описывается в файле dockerfile

`docker build` - команда создания образа _docker_

Конструкция команды сборки:

`docker build [-keys options] [path_to_dockerfile]`

Ключи:

**-t** - указывает тэг(название) _tag_ собирающегося образа

**--force-rm** - удаление промежуточных контейнеров

**-m** - установить ограничение памяти

**--pull** - всегда пытаться получить последнюю версию родительского образа

### Dockerfile

`FROM` - указать базовый образ на основе которого будет собираться новый

`LABEL` - описывает метаданные (важные сведения об образе)

`ENV` - устанавливает постоянные переменные среды в образе

`RUN` - выполняет команду и создает слой образа

`COPY` - копирует данные в контейнер

`ADD` - более функциональная версия _COPY_

`CMD` - команда с аргументами. выполняются сразу после запуска контейнера

`ARG` - задаёт переменные для передачи во время сборки

`WORKDIR` - задать рабочую директорию

`EXPOSE` - открыть порт

`ENTRYPOINT` - команда с аргументами для вызова во время выполнения контейнера

`VOLUME` - создает точку монтирования для работы с постоянным хранилищем

## Docker-compose

> [!NOTE]
>Так-же как и docker build, compose работает по сценарию описанному в docker-compose.yaml файле, для запуска необходимо находиться в папке где расположен сценарий docker-compose.yaml

`docker-compose up` - запускает сценарий docker-compose.yaml

`docker-compose ps` - показывает все запущенные, в настоящий момент, контейнеры docker-compose. При добавлении ключа -a покажет все контейнеры docker-compose которые (не запустились, были завершены, работают в настоящий момент, ... и т.д.)

`docker-compose stop` - останавливает контейнеры запущенные сценарием docker-compose.yaml

`docker-compose down` - останавливает и **УДАЛЯЕТ** контейнеры запусщенные в docker-compose сценарии.
