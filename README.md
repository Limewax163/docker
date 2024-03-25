# Help docker/docker-compose command

`docker ps -a` - показывает информацию о всех запущенных контейнерах (включая контейнеры запущенные в docker-compose сценариях)

`docker pull <docker_image>` - скачивание образа _из DockerHUB_

`docker image ls` - показывает все скаченные (pull) и собраные (build) образы

`docker rmi <id_image>` - удаляет выбранный образ по его <id_image>

`docker run ...` - запускает контейнер из скаченных, либо собранных в системе образов (image)

`docker volume ls` - покажет все созданные директории для докера

`docker volume rm <id_volume/name_volime>` - удаляет выбранную директорию либо по его <id_volume> либо по его <name_volume>

`docker network ls` - покажет все сети созданные для докера

> [!NOTE]
> Если необходимо явно задать рабочую сеть docker, можно воспользоваться правкой конфига (В данном случае отдаем docker сеть 172.17.0.0/16)
>
> 
> /etc/docker/daemon.json
> ```
> {
>        "default-address-pools":
>        [
>                {"base":"172.17.0.0/16","size":24}
>        ]
> }
> ```

`docker network rm <id-network/name-nwtwork>` - удаляет выбранную сеть либо по его `<id-network>` либо по его `<name-nwtwork>`

___

> [!NOTE]
>Для сборки образа необходим сценарий сборки, который описывается в файле dockerfile

`docker build` - команда создания образа _docker_

Конструкция команды сборки:

`docker build [-keys options] [path_to_dockerfile]`

Флаги:

`-t` - указывает тэг(название) _tag_ собирающегося образа

`--force-rm` - удаление промежуточных контейнеров

`-m` - установить ограничение памяти

`--pull` - всегда пытаться получить последнюю версию родительского образа

___

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

___

## Docker-compose

> [!NOTE]
>Так-же как и docker build, compose работает по сценарию описанному в docker-compose.yaml файле, для запуска необходимо находиться в папке где расположен сценарий docker-compose.yaml

`docker-compose up` - запускает сценарий docker-compose.yaml (При добавлении флага -d поднимет контейнеры в фоновом режиме)

`docker-compose ps` - показывает все запущенные, в настоящий момент, контейнеры docker-compose. (При добавлении флага -a покажет все контейнеры docker-compose которые не запустились, были завершены, работают в настоящий момент, ... и т.д.)

`docker-compose stop` - останавливает контейнеры запущенные сценарием docker-compose.yaml

`docker-compose down` - останавливает и **УДАЛЯЕТ** контейнеры запусщенные в docker-compose сценарии (если необходимо удалить вместе с контейнерами созданные директории,сети и все что создается по сценарию в автоматическом режиме можно выполнить команду с флагом -v)

`docker-compose logs` - покажет логи текущего docker-compose проекта
- при добавлении флага `-f` будет показывать все новые логи в текущем окне
- при добавлении флага `-t` привяжет хостовый таймаут логов
- при добавлении флага `--tail=<number>` покажет последние логи в колличестве `<number>`
