# Лабораторная работа №1: Docker
**Работа выполнена:** Бурматовой Е.С.
**ОС:** macOS (Apple Silicon)
**Program:** Docker

## Цель работы:
Научиться работать с Docker, выполнение практических заданий по Docker.

### Результаты выполненных заданий базового уровня:

**Task01. Проверка установки hello-world**
```bash
(base) katrin@MacBook-Pro-Katrin ~ % docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (arm64v8)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
**Контрольные вопросы:**
1. Что Docker сделал, когда образа hello-world не оказалось на машине?
2. Где он его взял?
3. Контейнер после вывода сообщения продолжил работать или завершился? Проверьте: docker ps и docker ps -a — в чём разница вывода?
**Ответы:**
1. The Docker daemon pulled the "hello-world" image from the Docker Hub. (arm64v8) – т.е. Docker взял образ из Docker Hub.
2. Docker Hub.
3. Завершился.

**Task 02. Интерактивный контейнер**
```bash
root@cb1c159fb183:/# ls
whoami
cat /etc/os-release | head -2
bin  boot  dev  etc  home  lib  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root
PRETTY_NAME="Ubuntu 26.04.1 LTS"
NAME="Ubuntu"
```
**Вопрос:**
Что произошло с контейнером после exit — почему он виден в docker ps -a, но не в docker ps?
**Ответ:**
После exit контейнер завершился, в docker ps -a мы видим статусы по всей истории запуска контейнеров, в docker ps увидим только текущие = работающие контейнеры.

**Task 03. Фоновый контейнер, stop и rm**
```bash
(base) katrin@MacBook-Pro-Katrin ~ % docker ps
curl http://localhost:8080
CONTAINER ID   IMAGE                                                   COMMAND                  CREATED              STATUS                  PORTS                                                             NAMES
6aa7112412f0   nginx                                                   "/docker-entrypoint.…"   About a minute ago   Up About a minute       0.0.0.0:8080->80/tcp, [::]:8080->80/tcp                           web
70cfe2ca8710   mongodb/mongodb-community-server:latest                 "python3 /usr/local/…"   4 hours ago          Up 4 hours              0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp                   mongodb
abb5bb8b524d   releases-docker.jfrog.io/jfrog/artifactory-oss:latest   "/usr/local/bin/fips…"   24 hours ago         Up 24 hours (healthy)   0.0.0.0:8081-8082->8081-8082/tcp, [::]:8081-8082->8081-8082/tcp   artifactory
8667b8c4bf01   postgres:16-alpine                                      "docker-entrypoint.s…"   24 hours ago         Up 24 hours (healthy)   5432/tcp                                                          artifactory-postgres
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
**Вопрос:**
Чем docker stop отличается от docker rm? Что останется после stop, но исчезнет после rm?
**Ответ:**
При выполнении docker stop происходит остановка работы контейнера с возможностью возобновления его работы, в случае с выполнением docker rm происходит удаление контейнера безвозвратно, т.е. повторный запуск совершить не удастся. После docker stop есть возможность увидеть контейнер с помощью docker ps -a, а после docker rm такой возможности нет.

**Task 04. Первый Dockerfile**
```bash
(base) katrin@MacBook-Pro-Katrin ~ % mkdir -p lab1/task04-dockerfile
(base) katrin@MacBook-Pro-Katrin ~ % cd lab1/task04-dockerfile
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % nano app.py
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % nano Dockerfile
```
**Задание:**
Подпишите для себя (в отчёт), что делает каждая инструкция: FROM, WORKDIR, COPY, CMD.
**Ответ:**

FROM **(базовый образ)** python:3.8-slim 

WORKDIR **(рабочая директория)** /app

COPY **(копирование файла)** app.py .

CMD **(команда при запуске)** ["python", "app.py"]

**Task 05. Сборка и запуск своего образа**
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker run hello-docker
Hello, Docker!
```
Эксперимент (пересборка)

```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % nano app.py
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker build -t hello-docker .
[+] Building 0.7s (8/8) FINISHED                                                    docker:desktop-linux
 => [internal] load build definition from Dockerfile                                                0.0s
 => => transferring dockerfile: 430B                                                                0.0s
 => [internal] load metadata for docker.io/library/python:3.8-slim                                  0.6s
 => [internal] load .dockerignore                                                                   0.0s
 => => transferring context: 2B                                                                     0.0s
 => [1/3] FROM docker.io/library/python:3.8-slim@sha256:1d52838af602b4b5a831beb13a0e4d073280665ea7  0.0s
 => => resolve docker.io/library/python:3.8-slim@sha256:1d52838af602b4b5a831beb13a0e4d073280665ea7  0.0s
 => [internal] load build context                                                                   0.0s
 => => transferring context: 70B                                                                    0.0s
 => CACHED [2/3] WORKDIR /app                                                                       0.0s
 => [3/3] COPY app.py .                                                                             0.0s
 => exporting to image                                                                              0.0s
 => => exporting layers                                                                             0.0s
 => => exporting manifest sha256:4eca8270d722ea446fb963e43d8e0ffa7571c1c5b17092fe04dafa3125a09ae1   0.0s
 => => exporting config sha256:6f94f108c6c614f272f8a1f00ebf56a7f9ae9acecd3962b656880fc8019d450c     0.0s
 => => exporting attestation manifest sha256:a659bd06f56d4ff7953f913057d6cd4cd53fdc39af1dc96770726  0.0s
 => => exporting manifest list sha256:defec85ab98657c23086f3645ab9707830c72352a024512b88ea968195d5  0.0s
 => => naming to docker.io/library/hello-docker:latest                                              0.0s
 => => unpacking to docker.io/library/hello-docker:latest                                           0.0s
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker images
                                                                                     i Info →   U  In Use
IMAGE                                                   ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-docker:latest                                     defec85ab986        215MB           47MB        
hello-world:latest                                      1e4c05b897a7       22.6kB         10.3kB    U   
mongodb/mongodb-community-server:latest                 f354c49227c0       1.49GB          395MB    U   
nginx:latest                                            689fbc6acfef        261MB         65.1MB        
postgres:16-alpine                                      81e77a483348        412MB          115MB    U   
releases-docker.jfrog.io/jfrog/artifactory-oss:latest   d103c0464d33       4.92GB         1.86GB    U   
ubuntu:latest                                           d66b366dac61        181MB         44.9MB    U   
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker run hello-docker
Hello, Docker! How are you?
```
**Вопрос:**
Какие шаги сборки пошли заново, а какие взялись из кэша (CACHED)?
**Ответ:**
Docker кэширует неизмененные слои, что значительно ускоряет повторную сборку. Слой [2/3] WORKDIR /app был взят из кэша (CACHED [2/3]), так как инструкция не менялась, слой [3/3] COPY app.py . был выполнен заново, так как исходный файл был изменен.

**Task 06. Тома: MySQL с постоянным хранилищем**
Вывод docker volume ls:
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker volume create mysql-data
docker volume ls
mysql-data
DRIVER    VOLUME NAME
local     99a3776b32050f3499d3115370e1fbb4ed4f1c224a98d8b766a37d1957cdb231
local     artifactory-lab-students_artifactory_data
local     artifactory-lab-students_postgres_data
local     f4776b3c332313863c268956c9a06462fa792a29834f02891bcbb020471140fb
local     mysql-data
```
Последние строки логов:
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker ps
docker logs mysql-container | tail -5
...
2026-11-20 12:39:05+00:00 [Note] [Entrypoint]: Stopping temporary server
2026-11-20 12:39:06+00:00 [Note] [Entrypoint]: Temporary server stopped

2026-11-20 12:39:06+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.
```

**Task 07. Данные переживают контейнер**
Вывод SHOW DATABASES после пересоздания контейнера:
```bash
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.00 sec)
```
**Вопрос:**
Где физически лежат данные (подсказка: docker volume inspect mysql-data)?
**Ответ:**
```bash
(base) katrin@MacBook-Pro-Katrin ~ % docker volume inspect mysql-data
[
    {
        "CreatedAt": "2026-11-20T12:37:28Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/mysql-data/_data",
        "Name": "mysql-data",
        "Options": null,
        "Scope": "local"
    }
]
```
После выполнения запроса docker volume inspect mysql-data в терминале становится понятно, что данные хранятся в томе Docker. Т.к. данные лежат в томе, удаление контейнера никак не влияет на них, а новый контейнер просто вытаскивает данные, потому что видит старую базу данных.

**Task 08. Своя сеть: Redis**
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker exec -it redis-server redis-cli ping
PONG

What's next:
    Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug redis-server
    Learn more at https://docs.docker.com/go/debug-cli/
```
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % docker network inspect my-network
[
...
        "Containers": {
            "6642e76068aac2b2eda0a7516fce60f5b68a9c41badc2e617bc5e949498bbcd4": {
                "Name": "redis-server",
                "EndpointID": "07217761cc334da35002a7a48470768258ec2060113cd1d2a0bdac844ec28dd0",
                "MacAddress": "2e:0a:dc:d1:3f:26",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
]
```

**Task 09. Python-клиенты к Redis**
Выполненные команды:
```bash
(base) katrin@MacBook-Pro-Katrin task04-dockerfile % cd ~
(base) katrin@MacBook-Pro-Katrin ~ % mkdir -p lab1/task09-redis
(base) katrin@MacBook-Pro-Katrin ~ % mv ~/Downloads/set_message.py ~/Downloads/get_message.py lab1/task09-redis/
(base) katrin@MacBook-Pro-Katrin ~ % cd lab1/task09-redis
(base) katrin@MacBook-Pro-Katrin task09-redis % ls -la
total 16
drwxr-xr-x  4 katrin  staff  128 20 нояб. 16:13 .
drwxr-xr-x  5 katrin  staff  160 20 нояб. 16:12 ..
-rw-r--r--@ 1 katrin  staff  180 20 нояб. 16:06 get_message.py
-rw-r--r--@ 1 katrin  staff  193 20 нояб. 16:06 set_message.py
(base) katrin@MacBook-Pro-Katrin task09-redis % nano set_message.py
(base) katrin@MacBook-Pro-Katrin task09-redis % docker run --rm --network my-network \
  -v $(pwd)/set_message.py:/set_message.py \
  python:3.8-slim bash -c "pip install -q redis && python /set_message.py"
Unable to find image 'python:3.8-slim' locally
3.8-slim: Pulling from library/python
Digest: sha256:1d52838af602b4b5a831beb13a0e4d073280665ea7be7f69ce2382f29c5a613f
Status: Downloaded newer image for python:3.8-slim
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

[notice] A new release of pip is available: 23.0.1 -> 25.0.1
[notice] To update, run: pip install --upgrade pip
Message set in Redis
(base) katrin@MacBook-Pro-Katrin task09-redis % nano get_message.py
(base) katrin@MacBook-Pro-Katrin task09-redis % docker run --rm --network my-network \
  -v $(pwd)/get_message.py:/get_message.py \
  python:3.8-slim bash -c "pip install -q redis && python /get_message.py"
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

[notice] A new release of pip is available: 23.0.1 -> 25.0.1
[notice] To update, run: pip install --upgrade pip
Message from Redis: Hello from Python
```
**Вопрос:**
Почему в скриптах указано имя redis-server, а не IP-адрес, и кто это имя разрешает?
**Ответ:**
В скриптах указываем redis-server, потому что контейнеры находятся в одной пользовательской сети Docker (my-network). Когда контейнер-клиент подключается к redis-server, Docker автоматически перехватывает этот запрос и перенаправляет его на актуальный внутренний IP-адрес контейнера Redis.

## Все действия отражены в прикрепленном файле:
