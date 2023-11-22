Help docker/docker-compose command

```
docker pull `<id_image>` - скачивание образа (из DockerHUB)
```
docker image ls - показывает все скаченные (pull) и собраные (build) образы
docker rmi <id_container> - удаляет выбранный образ по его <id>
docker run ... - запускает контейнер из скаченных, либо собранных в системе образов (image)
