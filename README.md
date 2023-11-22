Help docker/docker-compose command

скачивание образа (из DockerHUB)
```
docker pull <id_image>
```
показывает все скаченные (pull) и собраные (build) образы
```
docker image ls
```
удаляет выбранный образ по его <id>
```
docker rmi <id_container>
```

```docker run ...``` - запускает контейнер из скаченных, либо собранных в системе образов (image)
```
