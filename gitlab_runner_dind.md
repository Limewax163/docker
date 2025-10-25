Существует три варианта настройки ранера DIND для гитлаба
1. DonD - Докер использует удаленный сокет докера как свой (используя среду настроящего хозяина/хоста сокета
2. DinD - Докер подключается к удаленному сокету по порту 2375 либо 2376(с TLS) (используется удаленный сокет, но "среда выполнения" находится в контейнере докера)
3. какие-то сторонние штуки в виде kaniko, builder(buildx) или что-то еще. пока не разбирался

Опишу три варианта подключения. DonD и два DinD

1. DonD

Конфига раннера
```
[runners.docker]
  tls_verify = false
  image = "docker:28.5.1-dind-alpine3.22"
  privileged = true
  disable_entrypoint_overwrite = false
  oom_kill_disable = false
  disable_cache = false
  volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
  shm_size = 0
  network_mtu = 0
  network_mode = "host"
```
Для такого подхода в gitlab-ci указываем
```
variables:
  DOCKER_TLS_CERTDIR: ""
```
На этом настройка DonD завершена

2. DinD

Настраиваем сам докер к которому будем подключаться через системд

```
ExecStart=/usr/bin/dockerd \
        -H fd:// \
        -H tcp://0.0.0.0:2376 \
        --containerd=/run/containerd/containerd.sock \
        --tlsverify \
        --tlscacert=/data/CERT/docker/ca.pem \
        --tlscert=/data/CERT/docker/server-cert.pem \
        --tlskey=/data/CERT/docker/server-key.pem 
```
- Не обязательно слушать везде, можно поставить необходимый айпишник например 127.0.0.1
- Пути до сертов, саму-собой, соответствующие вашей конфигурации
- Если нужно без TLS просто убираем последние 4 строки (они не нужны) и порт 2375

затем
```
sudo systemctl daemon-reload && sudo systemctl stop docker && sudo systemctl start docker
```
- НЕ РЕСТАРТ, А ОСТАНОВИТЬ И ПОДНЯТЬ

2.1 DinD без TLS

Конфига раннера
```
[runners.docker]
  tls_verify = false
  image = "docker:28.5.1-dind-alpine3.22"
  privileged = true
  disable_entrypoint_overwrite = false
  oom_kill_disable = false
  disable_cache = false
  volumes = ["/cache"]
  shm_size = 0
  network_mtu = 0
  network_mode = "host"
```
- в данном примере указал с `network_mode = "host"` потому что это самый просто вариант убедиться что все корректно настроено и подключение к докеру есть. реализовать путь подключения можно и эллегантнее

В конфиге gitlab-ci
```
variables:
  DOCKER_HOST: "tcp://127.0.0.1:2375"
```
- данный метод подозревает использование ранее упомянутой настройки `network_mode = "host"`, для более эллегантных решений возможно необходимо будет сменить айпишник

2.2 DinD с TLS

Сперва генерим серты

Конфига раннера
```
[runners.docker]
  image = "docker:28.5.1-dind-alpine3.22"
  privileged = true
  disable_entrypoint_overwrite = false
  oom_kill_disable = false
  disable_cache = false
  volumes = ["/data/CERT/docker/remote_client_cert:/certs/client", "/cache", "/runner/services/docker"]
  shm_size = 0
  network_mtu = 0
```
- `/data/CERT/docker/remote_client_cert` я сюда положил серты для клиента, поэтому эту директорию замапил для контейнера раннера
- `"/runner/services/docker"` не уверен что это гавно необходимо

Конфига gitlab-ci
```
variables:
  DOCKER_HOST: "tcp://192.168.1.111:2376"
  DOCKER_TLS_VERIFY: true
  DOCKER_CERT_PATH: "/certs/client"
  DOCKER_TLS_CERTDIR: ""
  SHARED_PATH: /certs/client
```
- `SHARED_PATH` - не уверен что это гавно необходимо
- `DOCKER_CERT_PATH` - как раз та самая директория в которую маппились сертификаты котрые я сгенерил
