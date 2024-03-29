##### [Главная страница](../index.md)
##### [DevOps](index.md)
## [Docker](https://www.docker.com/)
### Установка
Согласно официальной [документации](https://docs.docker.com/install/linux/docker-ee/ubuntu/)
### Основные понятия
Инфраструктура docker реализуется за счет
* cgroups - распределение ресурсов (процессор, память)
* namespaces - пространства имен (изоляция пространств имен процессов, хостов, пользователей, изоляция сети)
* файловая система UnionFS - файловые системы накладываются друг на друга с перекрытием.
#### Хост
Хост - это компьютер на котором работает docker демон
#### Демон **docker**
Демон управляет образами и контейнерами (создание, запуск и т.п.).
Демон запускается командой **docker daemon**.
Запуск демона docker (systemd)
```bash
sudo systemctl start docker
```
#### Клиент docker
Клиент управляет демоном по протоколу HTTP (команда docker).
По каждой команде docker можно получить справку
```bash
docker <команда> --help
```
#### Image (образ)
Образ - это стек слоёв для чтения (части файловой системы UnionFS, доступные только для чтения).

Каждый слой - это результат выполнения команды в Dockerfile. 
Образы хранятся в реестрах образов, например [Docker hub](https://hub.docker.com/).
Локально скаченные из реестра образы хранятся в **/var/lib/docker**
#### Container (контейнер)
Контейнер - это экземпляр, развернутый из образа (образ плюс слой UnionFS доступный для записи).
#### Запущенный контейнер (экземпляр)
Запущенный контейнер - это контейнер с пространством процессов, портов и т.п.
#### Dockerfile
Dockerfile - это файл в котором описано, как строится образ.
По умолчанию при построении образа (docker build) берется Dockerfile из текущей директории.
#### Build context (контекст построения)
Buid context - это текущая рабочая папка на хосте относительно которой будут выполняться команды в Dockerfile.
В рабочей папке должны находится только файлы, необходимые для построения образа (build context передается демону docker и его большой размер замедлит построение образа).
### Работа с образами
#### Скачать образ
```bash
docker pull <репозиторий>:<образ>
```
Пример - получение образа последней (last) версии Ubuntu
```bash
docker pull ubuntu:last
```
#### Создание образа (последовательность команд **run** - создание и запуск контейнера и **commit** - фиксирование изменений как слоя только на запись для каждой команды из Dockerfile)
Для создания образа необходимо создать Dockerfile
```dockerfile
FROM <базовый образ>
VOLUME </путь к папке>
WORKDIR </абсоютный путь к директории в контейнере, которая будет текущим каталогом для всех последующих команд>
COPY <файл копируемый с хоста> </полный путь к конечному файлу в контейнере>
RUN <команды (через && \), добавляющие необходимые изменения в базовый образ>
ADD <файл копируемый с хоста (может быть tar архивом) или с URL> </полный путь к конечному файлу в контейнере>
EXPOSE <объявление используемых портов>
CMD <команда, запускающая первый процесс>
```
#### Копирование файлов
С хоста в образ можно копировать только те файлы которые поадают в текущий контекcт (по умолчанию это папка с файлом Dockerfile).
Команду COPY, с учетом предвартельно выполнненой команды WORKDIR, можно испльзовать так
```dockerfile
COPY . .
```
То есть все из каталога с файлом Dockerfile копируется в текущую диреторию в контейнере (текущая это та, в ктороую наc перевела команда WORKDIR)
Для копирования файлов в подпапку которой ещё не существует
```dockerfile
COPY file.txt /app/subfolder/
```
Для копирования нескольких файлов по шаблону необходимо указать конкретную директорию в кторую эти файлы коируются и указать в конце пути /
```dockerfile
COPY *.go /go/src/app/
```
> Если при сборке образа появляется ошибка типа failed to copy files: lstat /var/snap/docker/common/var-lib-docker/aufs/mnt/317b7b4de98894c6e725f2f32f0cff0096d5a1864ff4d5ec2545fa440d5dbf87/app/ra/config.yaml: not a directory, то надо проверить, что коируемый фал не указан в .dockerignore или что путь в котрый копируется файл (структура папок) сущесвтует.
#### Команды запуска
Вместо (а можно и совместно) CMD можно указать путь к скрипту в контейнере (ENTRYPOINT), который будет запускать первый процесс контейнера.
Существует 2 формы использвания команд RUN, CMD и ENTRYPOINT - (shell форма)
* RUN команда параметр - запуск с помощью оболочки (фактически как /bin/sh -c "<команда RUN в строке>")
* CMD ["команда", "параметр1", "параметрN"] - запуск без оболочки и её окружения (exec форма)
Также рекумендуется заавать программу в ENTRYPOINT, а её праметры в CMD
```dockerfile
ENTRYPOINT ["/usr/bin/nmap"]
CMD ["-sS", "localhost"]
```
#### WORKDIR
Команда WORKDIR создает папку, если её ещё нет.
#### Тома
Команда VOLUME создаст том на хосте и примонтирует его в указанную папку.
Также имя монируемого тома и папку в которую он монтируется на контейнере можно указать при запуске контейнера командой docker run (см. ниже).
#### Создание образа
Непосредственно создание образа из Dockerfile и присвоение ему тега (-t)
```bash
cd <папка с Dockerfile>
docker build -t <репозиторий/имя:версия>
docker commit
```
#### Список образов
```bash
docker images -aq
```
Опция **-a** позволяет увидеть все образы (не только базовые, полученные из репозитория), а опция **-q** оставляет в выводе только идентификаторы образов (это удобно использовать для передачи вывода списка образов в другие команды docker, например в команду удления образов).

Атрибуты образа
* ID - уникальный на локальном хосте идентификатор образа
* Tag - «человеческое» имя образа
* Репозиторий - имя репозитория из которого получен образ

#### Удалить образ
```bash
docker rmi <образ>
```
> Опция **-f** позволяет удалить образ принудительно (например, когда он требуется для контейнеров)
#### Удалить все обособленные (dangling) образы
Передача в команду docker вывода другой команды docker (или команды обалочки) как значения параметра - $()
```bash
docker rmi $(docker images -f "dangling=true" -q)
```
где dangling - это образ (обособленный), не связанный ни с каким образом, имеющим тег и не являющийся промежуточным образом (слоем) для других образов (то есть такие образы можно удялять, очищая место на диске).
#### Сохранить содержимое образа в архив
```bash
sudo docker save <образ> > образ.tar
```
Такой архив затем можно передать на другой хост и использовать там содержащийся в нем образ (распротсранение докер образа без репозитория)
### Работа с контейнерами
#### Список контейнеров
```bash
docker container ls -aq
```
или короче
```bash
docker ps ls -aq
```
Опция **-a** позволяет увидеть все контейнерв (не только запущенные), опция **-q** оставляет в выводе только идентификаторы контейнеров (это удобно использовать для передачи вывода списка образов в другие команды docker, например в команду адления контейнеров).
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
или (в alpine нет bash)
```bash
docker run -ti alpine sh
```
#### Запуск команды внутри запущенного контейнера
```bash
docker exec <ID контейнера> <команда>
```
#### Запуск контейнера в фоновом режиме (-d)
```bash
docker run <образ> -d
```
#### Создание и запуск контейнера с мапингом портов
```bash
docker run -p <порт на хосте>:<порт в контейнере> <образ>
```
> Порт в контейнере должен быть привязан к адресу 0.0.0.0, а не к localhost

#### Создание и запуск контейнера с внешним томом
```bash
docker --mount type=volume,source=<имя тома>,destination=/<путь к папке в контейнере> <образ>
````
где type=volume,source=<имя тома>,destination=/<путь к папке в контейнере> это параметр опции --mount

или через опцию -v без указаняи имя тома (устаревший синтаксис)
```bash
docker run -v </папка на хосте>:</папка в контейнере - точка монтирования> <образ>
```
или через опцию -v с указанием имени тома
```bash
docker run -v <том>:</папка в контейнере - точка монтирования> <образ>
```
или через опцию -v без указанием имени тома или папки на хосте (анонимный том, место размещения которого определит dcoker)
```bash
docker run -v /папка в контейнере - точка монтирования> <образ>
```
> Данные на томе будут сохранятся в файловой системе хоста, а не контейнера и при удалении контейнера данные не прорадут.

По умолчанию место хранение томов - **/var/lib/docker/volumes/**

Список томов
```bash
docker volume ls
```
#### Список контейнеров
```bash
docker ps
```
> Опция **-a** позволяет увидеть все контейнеры (не только запущенные)

Атрибуты контейнера
* ID - уникальный на локальном хосте идентификатор контейнера (новый при каждом запуске)
* Tag - «человеческое» имя (можно установить опцией **--name** при запуске контейнера)
#### Передача переменной окружения в контейнер
```bash
 docker run -e "ПЕРЕМЕННАЯ=<значение>" <образ>
```
#### Подключение к работающему контенеру
```bash
sudo docker exec -it <имя контейнера> /bin/bash
```
#### Остановить (SIG TERM) контейнер
```bash
docker stop <ID контейнера>
```
#### Остановить грубо (SIG KILL) контейнер
```bash
docker kill <ID контейнера>
```
#### Удалить контейнер (по сути - удаление слоя для записи)
```bash
docker rm <контейнер>
```
Контейнер можно удалить (--rm) после окончания его работы
```bash
docker run --rm <образ>
```
#### Останвоить все контейнеры, удалить их и все тома
```bash
docker container stop $(docker container ls -aq) && docker system prune -af --volumes
```
#### Информация о контейнере
```bash
docker inspect <ID контейнера>
```
#### Информация о дисковом пространстве, используемом docker
```bash
docker system df
```
### Работа с реестром (репозииторием)
#### Скачать образ
```bash
docker pull <репозиторий>/<тег>:<версия>
```
> по умолчанию версия - **latest** (последняя)

### Кэширование
Команды COPY, ADD, RUN добавляют слои и при этом добавляемые ими данные могут изменять образ от запуска к запуску (например, результат выполнения RUN git clone, изменит слой, если код в git репазитории изменился).
Добавляемые указанными выше командами в образ файлы сохраняются в кэше.
Если при выполнеии этих опираций добавляемые ими файлы, идентичны тем, что уже есть в кэше (наполнен предудщими выполнениями), то используются данные кэша.
Таким образом образ собирается быстрее.
Если на какой то команде каш применен не будет (есть изменения, относительно предудущей версии), то результаты всех последующих команд уже не будут браться из каша.
Отсюда рекомендация - выше в Dokerfile надо размещать команды, при выполнеии которых реже будет изменяться содержимое образа (и соответственно кэша).
Например, выше следует помещать добавление библиотек (пакетов, зависимостей) кода, а ниже сам исходный код.
Пример неоптимального использования кэша (если исходный код изменился,то зависимости тоже будут скачены заново, так как операция скачивания идет после операции копирования исходного кода)
```dockerfile
COPY requirments.txt .
COPY server.py
RUN pip install -r requirments.txt
```
Пример оптимального использотавния кэша (сначало получаем список зависимостей и устанавливаем их, затем получаем исходный код)
```dockerfile
COPY requirments.txt .
RUN pip install -r requirments.txt
COPY server.py
```
Также надо учитывать, что команды копирующие файлы по маске, не позволяют воспользоваться кэшированием
```dockerfile
COPY Gemfile* ./
```
Команда выше (и соответсвенно все что ниже нее) не использует кэш.
### Работа с томами
#### Просмотр содержимого тома на хосте
Просмотр метаинформации о томе
```bash
docker volume inspect <том>
```
В метаинформации содержится путь к папке в которой на хосте хранятся данные тома
### Multistaging
Multistaging - это создание итогового образа на основе промежуточного образа (или нескольких образов).
В общем случае берется образ в котором есть необходимые для создания приложения инструменты (например, для компиляции исполняемого файла), в нем готовится приложение, кторое затем переносится в новый образ где уже нет необходимых для создания приложения инструементов (они тут просто не нужны).
Этот способ хорошо подходит для создания образов с бинарными испоняемыми файлами (golang), которым для работы не нужны никакие дргуие компоненты ОС.
#### Просмотр содержимого контейнера, в основе которого лежит scratch образ  (образ без оболочки и утилит)
Сначало надо узнать PID процесса контейнера
```bash
sudo docker top контейнер
```
Также можно попробывать следующий вариант
```bash
PID=$(sudo docker inspect -f '{{.State.Pid}}' sleep)
cd /proc/$PID/root
ls
```
указанный выше способ не сработает на Mac, где надо
```bash
docker run --rm -it --pid container:sleep alpine
```
#### Заисимости бинарного приложения, помещаемого в образ, построенный на основе scratch
Необходимо из промежуточного образа скопировать в итоговый образ все библиотеки и файлы,требующиеся контейнерезируемому приложению или запускаемым им приложниям.
Посмотреть список библиотек, которые нужны исполняемому файлу можно так (эти файлы надо скопировать в готовый образ)
```bash
ldd приложение
```
Посмотерть какие относящиеся к пакету (программе) файлы установлены в системе можно так (эти файлы также надо скопировать в готовый образ)
для debian based (ubuntu)
```bash
 dpkg-query -L пакет
```
для alpine (также покажет все необходимые библиотеки)
```bash
pkg info -a пакет
```
#### Glibc
Вместо предложенно выше способа установки зависимостей в пустой scratch можно использовать образ в котором уже есть самые необходимые библиотеки (в том числе libc) и файлы необходимые для разрешения DNS имен и файлы с информацией пользователей (nsswitch, resolv.conf, passwd, groups)- **busybox:glibc**.
Также в этом образе есть оболочка и самые необходимые команды, что позволит проводить диагностику запущенного контейнера.
### Best practices (лучшие практики)
#### Использовтание конкретных версий образов и пакетов
Необходимо явно указывать версии (теги) используемых образов (не latest - это может быть каждый раз новая версия)
```bash
golang:1.15.2-buster
```
и явно указывать версии устанавлваемых пакетов с ПО (а желательно и репазиториев из которых это ПО брать)
```bash
apk add --no-cache bash==4.3.33-r0
```
или (не указываем патч-версию)
```bash
apk add --no-cache bash=4.3
```
#### Читаемость команд и минимизация промежуточных слоев
На самом деле указанные выше цели являются взаимоисключающими (одна команда оболочки в кажой строке с RUN) и минимизация слоев (в одной команде RUN несколько команд оболочки).
Поэтому возможен такой компромис - в каждой строке пишется одна команда оболочки или один устанавлеваемый apt-get, apk или yum пакет, но все они относятся к одной команде RUN
```bash
RUN mkdir -p /app/etc/ \
 mdir -p /var/lib/www /app/www
 cp  -p /etc/config.conf /app/config.conf
```
При перечислении пакетов для лучшей читаемости они указываются в алфавитном порядке
```bash
RUN apk add --no-cache \
 perl
 sudo
 tmux
```
#### Очитска кэша пакетного менеджера в каждом слое
Надо не забывать очищать кэш пакетного менеджера (скаченные для установки пакеты и т.п.) в каждом слое, то есть после каждой команды типа RUN apt-get install
```dockerfile
RUN apt-get install tmux  \
 && rm -rf /var/lib/apt/lists/*
RUN apt-get install sudo \
 && rm -rf /var/lib/apt/lists/*
```
#### Исопльзование кэша образа
Команды добавляющие в образ часто изменяемыое содержимое (исходный код) помещается ниже менее часто изменяющих содержимое образа команд (установка пакетов).
### Docker compose
По сути позволяет в одной конфигурации определить несколько команд по зпуску docker контейнеров, или дргуими словами - запускает несколько взаимосязанных сервисов (docker контейнеров).
#### Установка
```bash
sudo apt-get install docker-compose
```
#### Настройка
Можно комбинировать настройки конфигурационных файлов, указывая нексолько опций -f. Так опции будут дополнять друг друга (настройки в последенй опции переопределяют предыдущие).
### Основные команды
```bash
docker-compose build
docker-compose up -d
docker-compose down
docker-compose build logs -f сервис
docker-compose build ps
docker-compose exec
docker-compose images
```
#### docker-compose.yaml
Имена сервисов сановятся dns именами хостов, то есть в настройках в качестве имен серверов сервисов надо указывать имена сервисов.
#### Разворачивание контейнеров (запуск сервисов) из файла docker-compose.yml
```bash
sudo docker-compose up
```
#### Выполнение команды в одном из контейнеров (сервисе) файла  docker-compose.yml
```bASH
docker-compose run --rm app bundle exec rake db:create
```
#### Подключение к контейнеру (сервису) запущенному через docker-compose
Подключение происходит также, как и к контейнеру запущенному через docker run
```bash
docker exec -it 93bb2751bc5f sh
```
Указанная выше команда запустит и сервисы, указанные в зависимостях запускаемого сервиса.
### Ссылки
* Docker в картинках - [оригинал](http://merrigrove.blogspot.com/2015/10/visualizing-docker-containers-and-images.html), [перевод](https://habr.com/post/272145/)
* Проверка образов на уязвимости - [snyk.io](https://snyk.io/)
