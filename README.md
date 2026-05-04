# Домашнее задание «Практическое применение Docker»
Из - за технических сложностей (образ Питона автоматически не подгружался, БД автоматически не создавалась и выдавала пустые запросы) пришлось переделывать проект (использовать вместо MySQL MariaDB и вручнукю скачивать образ Питон), поэтому большая чать ответов в виде кода.

## Задание 0

Удаляем все пакеты docker-compose командой
```
root@docker-vm:/home/tankist# apt-get purge docker-compose-plugin -y
```
Псоле чего проверяем версию docker-compose и получаем ошибку:
```
root@docker-vm:/home/tankist# docker-compose --version
Command 'docker-compose' not found, but can be installed with:
snap install docker          # version 28.4.0, or
apt  install docker-compose  # version 1.29.2-1
See 'snap info docker' for additional versions.
```
Устанавливаем docker-compose командой
```
root@docker-vm:/home/tankist# apt-get install docker-compose -y
```
После чего проверяем наличие docker-compose:
```
root@docker-vm:/home/tankist# docker-compose --version
docker-compose version 1.29.2, build unknown
root@docker-vm:/home/tankist# docker-compose version
docker-compose version 1.29.2, build unknown
docker-py version: 5.0.3
CPython version: 3.10.12
OpenSSL version: OpenSSL 3.0.2 15 Mar 2022
```

## Задания 1 и 3

Репозиторий с проектом [тут](https://github.com/avdevninsr/shvirtd-example-python).</br>
Скачиваем докер образ Питона вручную, так как автоматически он не подгружается:
```
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker pull python:3.12-slim
3.12-slim: Pulling from library/python
3fc9d9ab5045: Pull complete
cb8c0a9140ac: Pull complete
4d873bcef452: Pull complete
3531af2bc2a9: Pull complete
75cf1a72ec4f: Download complete
67a6ed3b38af: Download complete
Digest: sha256:46cb7cc2877e60fbd5e21a9ae6115c30ace7a077b9f8772da879e4590c18c2e3
Status: Downloaded newer image for python:3.12-slim
docker.io/library/python:3.12-slim
```
Далее тестируем сборку проекта:
```
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker compose build
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/proxy.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/compose.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Building 57.5s (13/13) FINISHED
 => [internal] load local bake definitions                                                                       0.0s
 => => reading from stdin 564B                                                                                   0.0s
 => [internal] load build definition from Dockerfile.python                                                      0.0s
 => => transferring dockerfile: 354B                                                                             0.0s
 => [internal] load metadata for docker.io/library/python:3.12-slim                                              0.0s
 => [internal] load .dockerignore                                                                                0.0s
 => => transferring context: 126B                                                                                0.0s
 => [1/6] FROM docker.io/library/python:3.12-slim@sha256:46cb7cc2877e60fbd5e21a9ae6115c30ace7a077b9f8772da879e4  0.1s
 => => resolve docker.io/library/python:3.12-slim@sha256:46cb7cc2877e60fbd5e21a9ae6115c30ace7a077b9f8772da879e4  0.0s
 => [internal] load build context                                                                                0.0s
 => => transferring context: 56.46kB                                                                             0.0s
 => [2/6] RUN apt-get update && apt-get install -y     gcc     libpq-dev     && rm -rf /var/lib/apt/lists/*     21.8s
 => [3/6] WORKDIR /app                                                                                           0.1s
 => [4/6] COPY requirements.txt .                                                                                0.0s
 => [5/6] RUN pip install --no-cache-dir -r requirements.txt                                                    19.0s
 => [6/6] COPY . .                                                                                               0.1s
 => exporting to image                                                                                          16.2s
 => => exporting layers                                                                                         11.2s
 => => exporting manifest sha256:38494661cfecbcebe320f31ea0d8a08e05041c39a4b321ffa310344ea0fb2881                0.0s
 => => exporting config sha256:e74c5d7b4218af734cc48b6e334311d0e011010c198f00013254cad42b26b2c1                  0.0s
 => => exporting attestation manifest sha256:e426ab699c7c8af4684167c72b25fe8742f16f3a47952dcb642b71d2deec7911    0.0s
 => => exporting manifest list sha256:e430eb10e42fa67b18a1e35a1eb41a4def659d5f1bc32d7980d857e54b8b767e           0.0s
 => => naming to docker.io/library/web_app:latest                                                                0.0s
 => => unpacking to docker.io/library/web_app:latest                                                             4.9s
 => resolving provenance for metadata file                                                                       0.0s
[+] build 1/1
 ✔ Image web_app:latest Built                                                                                    57.6s
```
После успешной сборки проекта запускаем его:
```
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker compose up
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/proxy.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/compose.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] up 33/33
 ✔ Image nginx:latest                        Pulled                                                              21.4s
 ✔ Image mariadb:10.6.4-focal                Pulled                                                              25.3s
 ✔ Image haproxy:2.4                         Pulled                                                              16.0s
 ✔ Network shvirtd-project_backend           Created                                                              0.1s
 ✔ Volume shvirtd-project_db_data            Created                                                              0.0s
 ✔ Container mysql-db-compose                Created                                                              1.8s
 ✔ Container shvirtd-project-reverse-proxy-1 Created                                                              1.8s
 ✔ Container shvirtd-project-ingress-proxy-1 Created                                                              1.8s
 ✔ Container web-app-compose                 Created                                                              0.1s
```
Подключаемся к базе данных:
```
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker exec -ti 0ff1cc3a49f5 mysql -uroot -pYtReWq4321
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 8
Server version: 10.6.4-MariaDB-1:10.6.4+maria~focal mariadb.org binary distribution
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use virtd;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
```
Отправляем запросы на наличие таблиц и на выдачу IP адресов "посетителей" сайта:
```
MariaDB [virtd]> show tables;
+-----------------+
| Tables_in_virtd |
+-----------------+
| api_logs_v2     |
+-----------------+
1 row in set (0.000 sec)

MariaDB [virtd]> SELECT * from api_logs_v2 LIMIT 10;
+----+---------------------+---------------+
| id | request_date        | request_ip    |
+----+---------------------+---------------+
|  1 | 2026-05-01 07:42:47 | 192.168.0.101 |
|  2 | 2026-05-01 07:42:48 | 192.168.0.101 |
|  3 | 2026-05-01 07:42:50 | 192.168.0.101 |
|  4 | 2026-05-01 07:42:51 | 192.168.0.101 |
+----+---------------------+---------------+
4 rows in set (0.001 sec)
```
После всех необходимых действий гасим проект одной командой:
```
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker compose down
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/proxy.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
WARN[0000] /home/tankist/docker-project-2/shvirtd-example-python/compose.yaml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] down 5/5
 ✔ Container web-app-compose                 Removed                                                              0.5s
 ✔ Container shvirtd-project-reverse-proxy-1 Removed                                                              0.4s
 ✔ Container shvirtd-project-ingress-proxy-1 Removed                                                              0.2s
 ✔ Container mysql-db-compose                Removed                                                              0.5s
 ✔ Network shvirtd-project_backend           Removed                                                              0.2s
tankist@docker-debian:~/docker-project-2/shvirtd-example-python$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## Задание 4

Создаём ВМ в Yandex Cloud. Наличие ВМ продемонстрировано на рисунке 1.

<img src="img/2026-05-04_12-29-27.png" alt="Рисунок 1" width="auto" height="auto"></br>
Рисунок 1. ВМ в облаке.</br>
Далее устанавливаем на эту ВМ Докер и с помощью скрипта [deploy.sh](https://github.com/avdevninsr/shvirtd-example-python/blob/main/deploy.sh) разворачиваем и запускаем проект.</br>
Демонстрация того, что проект запущен представлена на рисунке 2.</br>

<img src="img/2026-05-04_12-26-48.png" alt="Рисунок 2" width="auto" height="auto"></br>
Рисунок 2. Проект запущен.</br>
Далее отправляем запрос к БД, что продемонстрировано на рисунке 3.</br>

<img src="img/%D0%91%D0%94%20%D0%B2%20%D0%BE%D0%B1%D0%BB%D0%B0%D0%BA%D0%B5%202.PNG" alt="Рисунок 3" width="auto" height="auto"></br>
Рисунок 3. Содержимое базы данных.</br>

## Задание 6

Команда docker dive не доступна по умолчанию, поэтому её необходимо установить:
```
root@docker-debian:~# DIVE_VERSION=$(curl -sL "https://api.github.com/repos/wagoodman/dive/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/')
curl -OL https://github.com/wagoodman/dive/releases/download/v${DIVE_VERSION}/dive_${DIVE_VERSION}_linux_amd64.deb
sudo apt install ./dive_${DIVE_VERSION}_linux_amd64.deb
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 3938k  100 3938k    0     0  2907k      0  0:00:01  0:00:01 --:--:-- 9278k
Чтение списков пакетов… Готово
Построение дерева зависимостей… Готово
Чтение информации о состоянии… Готово
Заметьте, вместо «./dive_0.13.1_linux_amd64.deb» выбирается «dive»
Следующие НОВЫЕ пакеты будут установлены:
  dive
Обновлено 0 пакетов, установлено 1 новых пакетов, для удаления отмечено 0 пакетов, и 3 пакетов не обновлено.
Необходимо скачать 0 B/4 033 kB архивов.
После данной операции объём занятого дискового пространства возрастёт на 9 863 kB.
Пол:1 /root/dive_0.13.1_linux_amd64.deb dive amd64 0.13.1 [4 033 kB]
Выбор ранее не выбранного пакета dive.
(Чтение базы данных … на данный момент установлено 196696 файлов и каталогов.)
Подготовка к распаковке …/dive_0.13.1_linux_amd64.deb …
Распаковывается dive (0.13.1) …
Настраивается пакет dive (0.13.1) …
N: Загрузка выполняется от лица суперпользователя без ограничений песочницы, так как файл «/root/dive_0.13.1_linux_amd64.deb» недоступен для пользователя «_apt». - pkgAcquire::Run (13: Отказано в доступе)
root@docker-debian:~# dive --version
dive 0.13.1
tankist@docker-debian:~$ dive hashicorp/terraform:latest
Image Source: docker://hashicorp/terraform:latest
Extracting image from docker-engine... (this can take a while for large images)
Analyzing image...
Building cache...
```
После установке сканируем скачанный образ Терраформа, что продемонстрировано на рисунке 4:

<img src="img/%D0%9A%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%B0%20dive.PNG" alt="Рисунок 4" width="auto" height="auto"></br>
Рисунок 4. Содержимое образа Терраформ.</br>
Как сказано на официальном сайте Докера: "Команда docker save не предназначена для сохранения конкретных файлов образа, она сохраняет образ исключительно целиком".</br>
```
tankist@docker-debian:~$ docker save -o terraform-latest.tar hashicorp/terraform:latest
```
Лично я нашёл ровно один способ вытащить искомый файл из архива:</br>
1) сначала я проверил состав архива:
```
tankist@docker-debian:~$ tar -tf terraform-latest.tar
blobs/
blobs/sha256/
blobs/sha256/015350b1d4482a767d4bab61ca8ee60e838b195e6fc6de8435a267bed267f610
blobs/sha256/1812937c038436596a004e3a8939625ae46e73db009caecc5428870263c0089d
blobs/sha256/2762c175fb20c644c31f5e783a1aa1de6b20537ad863b000c57649ae69bcb07c
blobs/sha256/76cbedd71a05bf6e8513645fbf8bf436d911348bb3f027280fb28a7476ed12d0
blobs/sha256/ba95657bb7499eb8fdde6f6a336f6fb5a3f6fe56e8aea644b35fc0be00660114
blobs/sha256/be527953936d1bcdf38826e50c2d44b28d1215df403e55248ba1f84ebd4c4467
blobs/sha256/fb609f4a116696050e0463bcabc81cc4a6819d7663c011f254a1ed7ccfda4c22
index.json
manifest.json
oci-layout
```
2) далее при помощи архиватора, поддерживающего функцию автоматической расшифровки зашифрованных архивов я "залез" в каждый из таких зашифрованных архивов, которые лежат в папке blobs. Я это произвёл следующим образом:</br>
2.1) при помощи ssh клинета (в моём случае MobaXTerm) скачал архив себе на хостовую ОС (Windows 8.1);</br>
2.2) отечественным архиватором 7Zip (поддерживает ли майкрософтовский WinRAR такое или нет я не знаю, встроенный в Дебиан 12 графическая версия 7Zip не умеет автоматически просматривать и извлекать зашифрованные файлы) прошёлся по папкам и нашёл нужную, это представлено на рисунке 5:<img src="img/%D0%98%D0%B7%D0%B2%D0%BB%D0%B5%D1%87%D0%B5%D0%BD%D0%B8%D0%B5.PNG" alt="Рисунок 5" width="auto" height="auto"></br>
Рисунок 5. Содержимое образа Терраформ.</br>
2.3) извлёк файл terraform без расширения;</br>
2.4) загрузил данный файл при помощи ssh клинета обратно на ВМ с Linux в папку /home/tankist/blobs/sha256/bin (естественно она была заранее создана);</br>
3) далее предоставляем полные права 777 (без этого не заработает) и проверяем версию Терраформа:
```
root@docker-debian:/home/tankist/blobs/sha256/bin# chmod 777 terraform
root@docker-debian:/home/tankist/blobs/sha256/bin# ./terraform version
Terraform v1.15.0
on linux_amd64
```
С помощью команды docker cp вытащить из образа нужные файлы можно двумя способами.</br>
Способ 1:
```
tankist@docker-debian:~$ docker create --name temp-terraform hashicorp/terraform:latest
52ee8f6e47db4e30923c8a69ef66c64b6e34655ec29478e3bf29077d7b2861f8
tankist@docker-debian:~$ docker cp  temp-terraform:/bin/terraform ./terraform
Successfully copied 117MB (transferred 117MB) to /home/tankist/terraform
tankist@docker-debian:~$ ls -la ./terraform
-rwxr-xr-x 1 tankist tankist 116932792 апр 29 17:18 ./terraform
tankist@docker-debian:~$ ./terraform --version
Terraform v1.15.0
on linux_amd64
```
Способ 2:
```
tankist@docker-debian:~$ docker run --rm --entrypoint /bin/sh hashicorp/terraform:latest -c "which terraform"
/bin/terraform
tankist@docker-debian:~$ docker create --name temp-terraform2 hashicorp/terraform:latest
cb002e8f1640d8bbf33d161c15caf4d24f14e0bed62d26a076006c91d2f2e781
tankist@docker-debian:~$ docker cp  temp-terraform2:/bin/terraform ./terraform
Successfully copied 117MB (transferred 117MB) to /home/tankist/terraform
tankist@docker-debian:~$ ./terraform version
Terraform v1.15.0
on linux_amd64
```
