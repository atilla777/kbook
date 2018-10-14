
## [Docker](https://www.docker.com/)
### Установка
Согласно официальной [документации](https://docs.docker.com/install/linux/docker-ee/ubuntu/)
### Основные понятия
Инфраструктура docker реализуется за счет
* cgroups - распределение ресурсво (процессор, память)
* namespaces - пространства имен (изоляция пространств имен процессов, хостов, пользователей, изоляция сети)
* файловая систему UnionFS - файловые системы накладываются друг на друга с перекрытием.
#### Демон **docker**
Демон управляет образами и контейнерами (создание, запуск и т.п.).
Демон запускается командой **docker daemon**.
Запуск демона docker (systemd)
```bash
sudo systemctl start docker
```
#### Клиент docker
Клиент управляет демоном (по ротоколу HTTP).
#### Image (изображение)
Образ - это стек слоёв для чтения (части файловой системы UnionFS , доступные только для чтения).

Каждый слой - это результат выполнения команды в Dockerfile. 

Локально скаченные из реестра образы хранятся в **/var/lib/docker**
Хранятся образы в реестрах образов, например [Docker hub](https://hub.docker.com/).
#### Container (контейнер)
Контейнер - это экземпляр, развернутый из образа (образ плюс слой UnionFS доступный для записи).

#### Запущенный контейнер (экземпляр)
Запущенный контейнер - это контейнер с пространством процессов, портов и т.п.
### Работа с образами
#### Скачать образ
```bash
docker pull <репозиторий>:<образ>
```
Пример - получение образа последней (last) версии Ubuntu
```bash
docker pull ubuntu:last
```
#### Создание образа (последовательность команд **run** - создание и запуск контейнера и **commit** - фиксирование изменений как слоя только на запись дл\ каждой команды из Dockerfile)
Для создания образа необходимо создать Dockerfile
```dockerfile
FROM <базовый образ>
COPY <файл копируемый с хоста> </полный путь к конечному файлу в контейнере>
RUN <команды (через && \), добавляющие необходимые изменения в базовый образ>
EXPOSE <объявление используемых портов>
CMD <команда, запускающая первый процесс>
```

> Вместо (а можно и совместно) CMD можно указать путь к скрипту в контейнере, который будет запускать первый процесс контейнера.

Непосредственно создание образа из Dockerfile
```bash
cd <папка с Dockerfile>
docker build
docker commit
```

#### Список образов
```bash
docker images
```
> Опция **-a** позволяет увидеть все образы

Атрибуты образа
* ID - уникальный на локальном хосте идентификатор образа
* Tag - «человеческое» имя образа
* Репозиторий - имя репозитория из которого получен образ

#### Удалить образ
```bash
docker rmi <образ>
```
> Опция **-f** позволяет удалить образ принудительно (например, когда он требуется для контейнеров)

### Работа с контейнерами
#### Создание контейнера (добавление к изображению слоя доступного для записи)
```bash
docker сreate <образ>
```
#### Запуск контейнера (процессы контейнера появляются в ОЗУ)
```bash
docker start <ID контейнера>
```
#### Создание и запуск контейнера (команды **create** и **start**)
```bash
docker run <образ>
```

> При запуске образа можно последним параметром указать команду (например, **bash**), которая запустится после запуска контейнера (это будет первый процесс контейнера с ID 0) и подключиться к терминалу контейнера (опция **-ti**)
```bash
docker run -ti ubuntu bash
```
#### Создание и запуск контейнера с мапингом портов
```bash
docker run -p <порт на хосте>:<порт в контейнере> <образ>
```
> Порт в контейнере должен быть привязан к адресу 0.0.0.0, а не к localhost

#### Список контейнеров
```bash
docker ps
```
> Опция **-a** позволяет увидеть все контейнеры (не только запущенные)

Атрибуты контейнера
* ID - уникальный на локальном хосте идентификатор контейнера (новый при каждом запуске)
* Tag - «человеческое» имя образа (можно установить опцией **--name** при запуске контейнера)

#### Удалить контейнер
```bash
docker rm <контейнер>
```
#### Остановить (SIG TERM) контейнер
```bash
docker stop <ID контейнера>
```

#### Остановить грубо (SIG KILL) контейнер
```bash
docker kill <ID контейнера>
```
### Работа с реестром (репозииторием)
#### Скачать образ
```bash
docker pull <репозиторий>/<тег>
```
> по умолчанию тег - **latest**

### Ссылки
* Docker в картинках - [оригинал](http://merrigrove.blogspot.com/2015/10/visualizing-docker-containers-and-images.html) , [перевод](https://habr.com/post/272145/)