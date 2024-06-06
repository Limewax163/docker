# Help docker/docker-compose command

- `docker build` - команда сборки образа docker
  - `-t` - указывает _tag_ собирающегося образа
  - `--force-rm` - удаление промежуточных контейнеров
  - `-m` - установить ограничение памяти
  - `--pull` - всегда пытаться получить последнюю версию родительского образа
- `docker ps` - показывает информацию о всех запущенных контейнерах (включая контейнеры запущенные в docker-compose сценариях)
  - `-a` покажет все контейнеры docker которые не запустились, были завершены, работают в настоящий момент, ... и т.д.
  - `-q` покажет только id контейнеров (полезно для выполнения команды используя конструкции bash как описано ниже)
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
  - `docker inspect --format "$(/path/to/tamplate.tpl)"`чтобы удобно распарсить вывод из файла в `docker run` команду. Сам шаблон [ТУТ](https://github.com/Limewax163/help_unix/tree/main/docker/parser-to-Docker_run.tpl)
- `docker login <url:port(if exist)>` - для логина в регистри гитлаба (если в ссылке указан порт, при логине его необходимо указывать)

### О проверке доступности памяти и чистке

- `docker system df` - Посмотреть сколько докер отожрал места, увидеть что докер может потенциально высвободить (отмеченное как устарелое/ненужное/неживое)
- `docker system prune` - Удаляет все ненужное (что мертво и не имеет отношения к работающему стеку)
  - `-a` - более серьезная очистка (возможно удалит данные которые потенциально могли бы использоваться в будущем)
- `docker volume prune` - Чистит мертвые/неиспользуемые volumes

___

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

___

<details>
  <summary>Взаимодействие с контейнерами docker</summary>

- `docker exec <flags> <container_name/id> <command>` - по сути подключение к docker с выполнением какой-то определенной команды внутри
  - `-i` - запуск команды в интерактивном режиме
  - `-t` - запуск команды с подключением к терминалу
  - `-u` -

</details>

___

<details>
  <summary>Настройки конфига Docker - <code>/etc/docker/daemon.json</code></summary>
  <pre>
{
    "default-address-pools":[{"base":"172.17.0.0/16","size":24}], - задается рабочая сетка докера
    "insecure-registries":["registry.gitlab.domain.ru"], - настройка недоверенных регистров контейнеров
    "registry-mirrors":["https://mirror.gcr.io"], - настройка зеркал для пула контейнеров
    "log-driver": "json-file", - настройки логирования
    "log-opts": {
      "max-size": "10m",
      "max-file": "3"
      }
}
  </pre>
</details>

___

### Шаблоны в docker-compose
Добавляются в определенно указанном месте/нескольких местах.
Удобно если для группы сервисов указываются одни и те же сущности.

<details>
  <summary>Конструкция шаблона в docker-compose</summary>

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

```

</details>

___

### Профили в docker-compose.
Для каждого сервиса может быть определен профиль. При запуске docker-compose будут подниматься только те сервисы, профиль которых будет указан при запуске (можно указать сразу несколько профилей)
Сервисы будут загружаться если профиль будет указан в `<.env>` 
```<.env>
PROFILE_NAME=production
```
либо если в команду `<docker-compose>` будет передан флаг с профилем например: `<docker-compose --profile <my_profile> up -d>`

<details>
  <summary>Объявление профиля в docker-compose</summary>

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

### Многострочные блоки в yaml
Многострочные блоки вызываются через `>` и `|` в дополнении к ним можно использовать несколько сценариев вывода информации из массива строк:

<details>
  <summary>Многострочные блоки</summary>

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

> [!NOTE]
> Экранировать символы в yaml можно повторяющимся символом. Например если необхоидмо экранировать `$` то двойной символ `$$` вернет `$` не ссылаясь на переменные среды.
