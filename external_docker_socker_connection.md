Сюда попали люди у которых это гавно действительно не работает, поэтому
1. Идем в системд (либо другой инит который используется. я рассматриваю вариант с системд) и смотрим кто вызывает докера
   ```
   $ sudo systemctl status docker
   ● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
   ```
2. Видим что докер стартует `/usr/lib/systemd/system/docker.service` и идем его редачить (вариант с `sudo systemctl edit docker` тоже нихрена не работает)

   Нас интересует секция [Service]
   ```
   [Service]
   Type=notify
   # the default is not to use systemd for cgroups because the delegate issues still
   # exists and systemd currently does not support the cgroup feature set required
   # for containers run by docker
   ExecStart=/usr/bin/dockerd \
        -H fd:// \
        -H tcp://0.0.0.0:2376 \
        --tlsverify \
        --tlscacert=/data/CERT/docker/ca.pem \
        --tlscert=/data/CERT/docker/server-cert.pem \
        --tlskey=/data/CERT/docker/server-key.pem \
        --containerd=/run/containerd/containerd.sock
   ```

   В данном примере указал вариант с настройкой сразу 2376 с TLS, если нужно небезопасно ходить то параметры сертов нас вобще не интересуют.
