<details>
  <summary><b>поставить</b></summary>

___

  <details>
    <summary><b>ubuntu version < 24.04.2</b></summary>

-
    ```
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    ```
- 
    ```
    echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```
-
    ```
    sudo apt update
    sudo apt-get install docker-ce docker-ce-cli docker-compose containerd.io docker-buildx-plugin docker-compose-plugin
    ```

  </details>

___

  <details>
    <summary><b>Установить alias</b></summary>

  - docker < v.28

    ```
    sudo mv /bin/docker-compose /bin/docker-compose-BACKUP && sudo sh -c "echo 'docker compose "$@"' > docker-compose"
    ```
  - docker >= v.28
    
    ```
    sudo mv /usr/local/bin/docker-compose /usr/local/bin/docker-compose-BACKUP && sudo ln -f -s /usr/libexec/docker/cli-plugins/docker-compose /usr/local/bin/docker-compose
    ```

  </details>

___

</details>

<details>
  <summary><b>docker/docker-compose command</b></summary>

- `docker info` - посмотреть главную конфигу docker (изменение стандартных конфигураций описывается в файле /etc/docker/daemon.json)  
- `docker build` - команда сборки образа docker
  - `-t` - указывает _tag_ собирающегося образа
  - `--force-rm` - удаление промежуточных контейнеров
  - `-m` - установить ограничение памяти
  - `--pull` - всегда пытаться получить последнюю версию родительского образа
  - `--progress=plain` - показать подробный вывод при билде в новой версии докер
- `docker ps` - показывает информацию о всех запущенных контейнерах (включая контейнеры запущенные в docker-compose сценариях)
  - `-a` покажет все контейнеры docker которые не запустились, были завершены, работают в настоящий момент, ... и т.д.
  - `-q` покажет только id контейнеров (полезно для выполнения команды используя конструкции bash как описано ниже)
- `docker pull <docker_image>` - скачивание образа _из DockerHUB_
- `docker image ls` - показывает все скаченные (pull) и собраные (build) образы
- `docker rmi <id_image>` - удаляет выбранный образ по его <id_image>
- `docker run <id_image:tag>` - запускает контейнер из скаченных, либо собранных в системе образов (image)
- `docker volume ls` - покажет все созданные директории для докера
- `docker volume rm <id_volume/name_volime>` - удаляет выбранную директорию либо по его <id_volume> либо по его <name_volume>
- `docker network ls` - покажет все сети созданные для докера
- `docker network rm <id-network/name-nwtwork>` - удаляет выбранную сеть либо по его `<id-network>` либо по его `<name-nwtwork>`
- `docker ps -s --format "table {{.Names}}\t{{.Size}}" | sort -k2 -h -r` покажет сколько занимают памяти контейнеры с выводом по шаблону только имя контейнера и занятую память с сортировкой по убыванию занятой памяти
- `docker rm -f $(docker ps -aq) && docker rmi -f $(docker images -q)` удаляет все образы и контейнеры
- `docker inspect <container_id>` - информация по запущенному контейнеру
  - `--format` с флагом поддерживает шаблоны в формате json для парсинга
  - `docker inspect --format "$(/path/to/tamplate.tpl)"`чтобы удобно распарсить вывод из файла в `docker run` команду. Сам шаблон [ТУТ](https://github.com/Limewax163/docker/blob/main/tamplate/inspect-to-compose.tpl) либо можно взять шаблон из сети, тогда `$(curl -s <url>)`
- `docker login <url:port(if exist)>` - для логина в регистри гитлаба (если в ссылке указан порт, при логине его необходимо указывать)
- `docker exec <flags> <container_name/id> <command>` - подключение к docker с выполнением какой-то определенной команды внутри
  - `-i` - запуск команды в интерактивном режиме
  - `-t` - запуск команды с подключением к терминалу
  - `-u` - вход под определенным пользователем
- `docker cp /path/to/local/file container_id:/path/to/destination` - для копирования файла в контейнер докера
- `docker save -o my-image.tar my-image:latest` - сохранить образ в tar
- `docker load -i my-image.tar` - подгрузить образ из tar в локальную библиотеку докера
___

- `docker-compose up` - поднимает контейнеры из описанного в текущей директории конфига compose
  - `-d` - отвязывает консоль
  - `-f` - явно указать путь и название до конфига .yml (флаг применим к множеству команд где идет взаимодействие с файлом конфигурации compose
- `docker-compose down` - роняет контейнеры из описанного в текущей директории конфига compose
  - `-v` - дополнительно чистит volumes
- `docker-compose start/stop/restart` - запускает/останавливает/перезапускает контейнеры из описанного в текущей директории конфига `docker-compose.yml` (более мягкая перезагрузка нежели down/up)
  - `docker compose start/stop/restart <container_name>` - можно работать с отдельными сервисами явно указав их после команды
- `docker-compose logs <service_name>` - показывает логи контейнеров из композа. Если не указывать <service_name> из композа, покажет логи по всем сервисам композа.
  - `-f` - привязывает консоль к логам
  - `-t` привяжет хостовый таймаут логов
  - `--tail=<number>` покажет последние логи в колличестве `<number>` строк
- `docker-compose ps` - покажет информацию по контейнерам от определенного композа
  - `-a` покажет все контейнеры docker-compose которые не запустились, были завершены, работают в настоящий момент, ... и т.д. 

### О проверке доступности памяти и чистке

- `docker system df` - Посмотреть сколько докер отожрал места, увидеть что докер может потенциально высвободить (отмеченное как устарелое/ненужное/неживое)
- `docker system prune` - Удаляет все ненужное (что мертво и не имеет отношения к работающему стеку)
  - `-a` - более серьезная очистка (возможно удалит данные которые потенциально могли бы использоваться в будущем)
- `docker volume prune` - Чистит мертвые/неиспользуемые volumes

</details>

<details>
  <summary><b>Фильтры для команд докера</b></summary>
Если необходимо выдернуть какие-то определенные столбцы можно использовать <code>--format</code> флаг
<details><summary>Варианты</summary>{{.Image}}  {{.ID}}  {{.Image}}  {{.Command}}  {{.RunningFor}}  {{.Status}}  {{.Ports}}  {{.Names}}</details>
На примере образа и имени
<pre><code>❯ docker ps --format "table {{.Image}}\t{{.Names}}" container_id
IMAGE                                                     NAMES
harbor.limewax.ru/limewax/zabbix-nginx-pgsql:7.0          zabbix_dashboard_1
harbor.limewax.ru/limewax/zabbix-server-pgsql:7.0         zabbix_server_1
harbor.limewax.ru/limewax/redis:7.2.5                     zabbix_redis_1
harbor.limewax.ru/limewax/postgres:15                     zabbix_postgres_1</code></pre>

Либо в качестве шаблона можно использовать файл:
<pre><code>❯ docker ps --format "(cat /path/to/tamplate.tpl)" container_id</code></pre>

Можно фильтровать по определенным зависимостям
  - тут вернет все контейнеры со статусом exited
  ```
  docker ps -a --filter "status=exited/running/другие статусы"
  ```

</details>

> [!NOTE]
> Экранировать символы в yaml можно повторяющимся символом. Например если необхоидмо экранировать `$` то двойной символ `$$` вернет `$` не ссылаясь на переменные среды.
___

<details>
  <summary><b>Интерполяции переменных</b></summary>

В docker-compose.yml можно указать переменную, которая будет считываться с учетом определенных условий:

- `${VAR:-default}` - Возвращает `<VAR>` если он существует в файле конфигурации И имеет значение (Пустая переменная не считывается). В остальных случаях используется `<default>`
- `${VAR-default}` - Возвращает `<VAR>` если он существует в файле конфигурации (Может считываться пустая переменная). В остальных случаях используется `<default>`
- `${VAR:?error}` - Возвращает `<VAR>` если он существует в файле конфигурации И имеет значение (Пустая переменная не считывается). В остальных случаях получаем ошибку
- `${VAR?error}` - Возвращает `<VAR>` если он существует в файле конфигурации (Может считываться пустая переменная). В остальных случаях получаем ошибку
- `${VAR:+replacement}`
- `${VAR+replacement}`
</details>

___


<details>
  <summary><b>Плагины для Docker</b></summary>

- `docker plugin install <docker_plugin_url> --alias <plugin_name> --<grant_all_permissions>` - установка плагина (--alias создает короткое имя для применения плагина в конфигурациях докера --grant_all_permission добавляет права для плагина)
- `docker plugin ls` - посмотреть список установленых плагинов
- `docker plugin <disable/enable> <plugin_name>` - включить отключить плагин
- `docker plugin upgrade <plugin_name> <docker_plugin_url> --<some_specified_flags>` - обновление плагина (перед обновлением плагин отключается, затем обновляется, затем включается, затем перезагружается демон докера) 

  <details>
    <summary><b>Loki - Драйвер логирования для Docker</b></summary>

    ```
    docker plugin install grafana/loki-docker-driver:3.3.2-arm64 --alias loki --grant-all-permissions
    ```

  </details>
</details>

___


<details>
  <summary><b>Основные сущности Dockerfile</b></summary>
  
- `FROM` - указать базовый образ на основе которого будет собираться новый
  <details>
    <summary>FROM</summary>
  - `AS base` указывает на главный образ в контексте сборки, далее если в рамках этого докерфайла собирается еще один контейнер можно указать его как `FROM <target>` что будет эквивалентно предидущей сборке (в последних версиях докера стал ругаться на регистр у AS (необходимо использовать верхний регистр чтобы небыло ошибок)

  ```
  Dockerfile
  FROM image:tag AS base
  ...
  FROM base AS <target>
  ```
  В docker-compose в дальнейшем можно указать с какого этапа будет пересобираться контейнер
  ```
  docker-compose.yml
  services:
    app:
      build:
        target: <target>
  ```
    </details>
    
- `LABEL` - описывает метаданные (важные сведения об образе)
- `ENV` - устанавливает постоянные переменные среды в образе
- `RUN` - выполняет команду и создает слой образа
- `COPY` - копирует данные в контейнер
  <details>
  <summary>COPY</summary>
  - Если Dockerfile включает в себя несколько этапов сборки AS/FROM, то можно передать определенные данные через COPY из одной сборки в другую, например:

  ```
  # build stage
  FROM node:14 as build-stage
  ...
  RUN npm run build #Билдятся файлы в определенную папку, например /usr/src/app/dist
  # production stage
  FROM nginx:stable-alpine as production-stage
  COPY --from=build-stage /usr/src/app/dist /usr/share/nginx/html #Из стадии билда забираются статичные файлы для nginx и стартует контейнер nginx с уже готовой сборкой фронта
  ```
  </details>
- `ADD` - более функциональная версия _COPY_
- `CMD` - команда с аргументами. выполняются сразу после запуска контейнера
- `ARG` - передает переменные в сборку (например из docker-compose)
    <details>
  <summary>ARG</summary>
  - В Dockerfile можно указать ARG (например USER) и далее после объявленного ARG в переменную USER можно вызывать ее в контексте сборки как $USER. Подкинуть эту переменную можно из docker-compose файла в котором будет указанно значение для этой переменной (в рамках docker-compose можно так же указать забирать эту переменную из .env объявив в качестве аргумента переменную в .env $USER:

  ```
  .env
  CONTAINER_USER=MY_AWESOME_USER
  ```
  ```
  Dockerfile
  FROM <image>
  ARG CONTAINER_USER
  RUN adduser $CONTAINER_USER
  ```
  ```
  docker-compose.yml
  service:
    nginx:
    build:
      context: /path/to/Dockerfile
      args:
        USER: "${CONTAINER_USER}"
  ...
  ```
  </details>

- `WORKDIR` - задать рабочую директорию
- `EXPOSE` - открыть порт
- `ENTRYPOINT` - команда с аргументами для вызова во время выполнения контейнера
- `VOLUME` - создает точку монтирования для работы с постоянным хранилищем

</details>

___

<details>
  <summary><b>Настройки конфига Docker</b> - <code>/etc/docker/daemon.json</code></summary>
  <pre>
{
    "data-root": "/data/docker",
    "default-address-pools":[{"base":"100.100.0.0/23","size":28}], - задается рабочая сетка докера. Докер будет нарезать подсети из пула 100.100.0.0/25 (128 Доступных адресов в сети) и для каждой подсети будет выделять максимум 16 адресов (из них 14 адресов для использования, 1 — адрес сети, 1 — широковещательный адрес) что в итоге позволит иметь 8 подсетей внутри сети 100.100.0.0/25
    Лучше не юзать это гавно а просто использовать "default-address-pools". Первый айпишник из сети будет шлюзом. "bip": "100.100.1.1/26", - задается адрес шлюза в сеть/из сети докера (не должен пересекаться с сетями выделяемыми для контейнеров из конфигурации выше)
    "insecure-registries":["registry.gitlab.domain.ru"], - настройка недоверенных регистров контейнеров
    "registry-mirrors":["https://mirror.gcr.io"], - настройка зеркал для пула контейнеров
    "ipv6": false,
    "dns": ["8.8.8.8", "8.8.4.4"], - явное указание DNS для docker
    "metrics-addr": "0.0.0.0:9323", - если необходимо отправить метрики докера на определенный порт
    "experimental": true, - включение экспереминтальных функций
    "live-restore": true, - продолжает работу контейнеров, если демон становится недоступным
    "log-driver": "json-file", - настройки логирования
    "log-opts": {
      "max-size": "10m",
      "max-file": "3"
      }
    }
    "features": {
            "containerd-snapshotter": false - включается для buildix и сборки мультиплатформенных образов (незабыть про флаг --provenance=false)
            "buildkit": false - включается для buildix и сборки мультиплатформенных образов
    }
}
  </pre>
</details>

___

<details>
  <summary><b>Шаблоны в docker-compose (yml-anchor)</b></summary>

- Указывается шаблон `<x-tamplate>`, затем указывается имя шаблона. В шаблоне может быть сколько угодно сущностей. Затем шаблон вставляется в необходимое место с помощью `<<: *<tamplate_name>`

```
version: <compose_version>

############ tamplate ##############
x-tamplate: &<tamplate_name>
  restart: unless-stopped
  networks:
    - network

#use it with - <<: *<tamplate_name>
####################################

service:
  image: nginx:latest
  <<: *<tamplate_name>
...
```

</details>

___

<details>
  <summary><b>Объявление профиля в docker-compose</b></summary>
> Для каждого сервиса может быть определен профиль. При запуске docker-compose будут подниматься только те сервисы, профиль которых будет указан при запуске (можно указать сразу несколько профилей)
Сервисы будут загружаться если профиль будет указан в `<.env>` 
```<.env>
PROFILE_NAME=production
```
>либо если в команду `<docker-compose>` будет передан флаг с профилем например: `<docker-compose --profile <my_profile> up -d>`


```
  adminer:
    profiles: [production]
    image: adminer
    ports:
      - "8880:8080"
    networks:
      - network
```

</details>

___

<details>
  <summary><b>Многострочные блоки в YAML</b></summary>
Многострочные блоки вызываются через `>` и `|` в дополнении к ним можно использовать несколько сценариев вывода информации из массива строк:

Вывод строк сохраняется как есть (учитываются переходы на новую строку)
```
steps:
  - label: "Build the app"
    key: "build"
    command: |
      echo "--- Install gems"
      bundle install
      echo "--- Build the app"
      bundle exec fastlane build
```
Вывод строк заменяет все переходы на новую строку пробелом в следствии чего все что записанно в данном варианте будет выведено одной строкой.
```
notify:
  - slack:
      channels: ["#build-notifs"]
      message: >
        Your build have failed. You might want to check your
        CI logs for more details about the failure, or ping
        your friendly neighbourhood Infrastructure Engineer
        on call to ask for help.
    if: build.state == "failed"
```
Несколько вариантов использования дополнительных опций и примеры вывода
```
examples:
  clip: >
    This content will end with a LF character
    but not include the final empty lines.
    
    
  strip: >-
    This content will neither contain a trailing LF character
    nor the trailing empty line.
    
  keep: >+
    This content will keep both the LF
    and the trailing empty lines.
    
    
equivalent-output:
  clip: "This content will end with a LF character but not include the final empty lines.\n"
  strip: "This content will neither contain a trailing LF character nor the trailing empty line."
  keep: "This content will keep both the LF and the trailing empty lines.\n\n\n"
```

Дополнительно можно посмотреть представление [`ТУТ`](https://yaml-multiline.info/)

</details>
