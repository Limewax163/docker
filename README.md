# Help docker/docker-compose command


`docker pull `<id_image> - скачивание образа _из DockerHUB_

`docker image ls` - показывает все скаченные (pull) и собраные (build) образы

`docker rmi `<id_image> - удаляет выбранный образ по его <id_image>

`docker run `... - запускает контейнер из скаченных, либо собранных в системе образов (image)

# Dockerfile

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

