# Help docker/docker-compose command

<details>
  <summary><code>docker ps</code> - показывает информацию о всех запущенных контейнерах (включая контейнеры запущенные в docker-compose сценариях)</summary>

- при добавлении флага `-a` покажет все контейнеры docker которые не запустились, были завершены, работают в настоящий момент, ... и т.д.
- при добавлении флага `-q` покажет только id контейнеров (полезно для выполнения команды используя конструкции bash как описано ниже)

</details>

`docker pull <docker_image>` - скачивание образа _из DockerHUB_

`docker image ls` - показывает все скаченные (pull) и собраные (build) образы

`docker rmi <id_image>` - удаляет выбранный образ по его <id_image>

`docker run ...` - запускает контейнер из скаченных, либо собранных в системе образов (image)

`docker volume ls` - покажет все созданные директории для докера

`docker volume rm <id_volume/name_volime>` - удаляет выбранную директорию либо по его <id_volume> либо по его <name_volume>

> [!NOTE]
> При любой команде удаления можно воспользоваться конструкцией bash скрипта, для удаления группы контейнеров. Например:
> `docker rmi ${docker image ls}` или `docker rm ${docker ps -qa}` отправляя в `$` все 

`docker network ls` - покажет все сети созданные для докера

> [!NOTE]
> Если необходимо явно задать рабочую сеть docker, можно воспользоваться правкой конфига /etc/docker/daemon.json

<details>
  <summary><code>daemon.json</code></summary>
  <pre>
{
    "default-address-pools":
    [
        {"base":"172.17.0.0/16","size":24}
    ]
}
  </pre>
</details>

`docker network rm <id-network/name-nwtwork>` - удаляет выбранную сеть либо по его `<id-network>` либо по его `<name-nwtwork>`

___

> [!NOTE]
>Для сборки образа необходим сценарий сборки, который описывается в файле Dockerfile

`docker build` - команда создания образа _docker_

Конструкция команды сборки:

<details>
  <summary><code>docker build [-keys options] [path_to_dockerfile]</code></summary>

- при добавлении флага `-t` - указывает тэг(название) _tag_ собирающегося образа
- при добавлении флага `--force-rm` - удаление промежуточных контейнеров
- при добавлении флага `-m` - установить ограничение памяти
- при добавлении флага `--pull` - всегда пытаться получить последнюю версию родительского образа

</details>

### Dockerfile

<details>
  <summary>Основные сущности Dockerfile</summary>
  
- `FROM` - указать базовый образ на основе которого будет собираться новый
- `LABEL` - описывает метаданные (важные сведения об образе)
- `ENV` - устанавливает постоянные переменные среды в образе
- `RUN` - выполняет команду и создает слой образа
- `COPY` - копирует данные в контейнер
- `ADD` - более функциональная версия _COPY_
- `CMD` - команда с аргументами. выполняются сразу после запуска контейнера
- `ARG` - задаёт переменные для передачи во время сборки
- `WORKDIR` - задать рабочую директорию
- `EXPOSE` - открыть порт
- `ENTRYPOINT` - команда с аргументами для вызова во время выполнения контейнера
- `VOLUME` - создает точку монтирования для работы с постоянным хранилищем

</details>

## Docker-compose

> [!NOTE]
>Так-же как и docker build, compose работает по сценарию описанному в docker-compose.yaml файле, для запуска необходимо находиться в папке где расположен сценарий docker-compose.yaml

`docker-compose up` - запускает сценарий docker-compose.yaml
- при добавлении флага `-d` поднимет контейнеры в фоновом режиме

`docker-compose ps` - показывает все запущенные, в настоящий момент, контейнеры docker-compose
- при добавлении флага `-a` покажет все контейнеры docker-compose которые не запустились, были завершены, работают в настоящий момент, ... и т.д.

`docker-compose stop` - останавливает контейнеры запущенные сценарием docker-compose.yaml

`docker-compose down` - останавливает и **УДАЛЯЕТ** контейнеры запусщенные в docker-compose сценарии
- при добавлении флага `-v` удалит все прокинутые на хост и созданые `volumes` `networks` 

`docker-compose logs` - покажет логи текущего docker-compose проекта
- при добавлении флага `-f` будет показывать все новые логи в текущем окне
- при добавлении флага `-t` привяжет хостовый таймаут логов
- при добавлении флага `--tail=<number>` покажет последние логи в колличестве `<number>`
