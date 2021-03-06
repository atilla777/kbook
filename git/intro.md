##### [Git](index.md)
##### [Главная страница](../index.md)
## Git - общие сведения
В папке проекта приложения, находящегося под контролем git (в которой инициализирован git репозиторий) содержатся
* непосредственно папка репозитория - .git
* рабочий каталог.
Каждый комит git это фактически слепок файловой системы рабочей директории (тех файлов которые были проиндексированы), на момент выполнения комита. Если файл не менялся относительно предыдущего комита, то в слепке (комите) будет храниться не сам файл, а ссылка на него в предыдущем слепке (комите).
Отслеживаемые файлы рабочего каталога, файлы вошедшие в последний комит.
Область подготовленных файлов (staging area) представляет собой файл .git/index. В этом файле содержится информация о том, какие изменения (файлы) войдут комит (проиндексированы).
#### Типы объектов git
В репозитории git (папка .git) существуют следующие типы объектов
* Blob (файлы в двоичном виде)
* Комиты
* Деревья
* Метки

Каждый объект сохраняется в папке, помещенной в папку objects и название которой состоит из двух букв хэша (SHA-1) объекта. Сам объект помещается в файл имя которого состоит из остальных символов хэша объекта.
Посмотреть содержимое объекта можно командой
```bash
git show <первые четыре символа хэша объекта>
```
Объекты хранятся в двоичном виде, что, при сохранении разных версий файла позволяет сохранять только разницу между этими двумя файлами.
#### Деревья
Деревья предназначены для хранения информации о структуре файловой системы рабочей области git на время определенного комита.

При каждом новом комете создается новое дерево в котором зафиксировано состояние депозитария на момент комита.
Дерево представляет собой папку файловой системы и содержит информацию о содержащихся в них других деревьев (папок), а также blob объектов (файлов).
Каждое дерево содержит следующую информацию о входящих в нее объектах
* Тип
* Хэш
* Имя

Просмотр содержимого дерева
```bash
git ls-tree хэш
```
#### Комиты
Каждый комит содержит следующую информацию
* Хэш дерева проиндексированного на момент комита (дерево в свою очередь содержит ссылки (хэши) на файлы и на дочерние деревья)
* Данные комитера (имя и email)
* Комментарий
* Ссылку (хэш) на родительский комит

В файле HEAD хранится ссылка на файл текущей ветки.
В свою очередь в файле refs/head/ветка хранится хэш текущего комита (то есть то, куда реально ссылается HEAD).
При переключении между ветками (git checkout ветка) происходит изменение ссылки в HEAD на последний комит в заданной ветке (ссылка в файле refs/head/ветка).
Если переключится с одной ветки на другую до того как сделать комит, то, если конфликта в изменениях нет, то изменения в рабочей папке перейдут в ветку на которую 
(git checkout ветка) происходит изменение ссылки в HEAD на последний комит в заданной ветке (ссылка в файле refs/head/ветка).

Если переключится с одной ветки на другую до того как сделать комит, то
* если конфликта в изменениях нет, то изменения в рабочей папке перейдут в ветку на которую произошло переключение (незакомиченные и перетянутые изменения, после выполнения команды git checkout будут помечены буквой M)
* если конфликт есть, переключение не произойдет. В этом случае можно форсировать переход ключем –m
```bash
git checkout ветка –m
```
В этом случае оба изменения войдут файл (они будут соответствующим образом помечены), и  текстовом редакторе можно будет выбрать нужное изменение.
Рекомендуется делать комит перед переходами между ветками.
Если все же надо переключиться между ветками, до того как работа в текущей ветке закомичена, можно проиндексировать изменения командой git add ., сохранить состояние рабочей директории командой git stash, переключиться на нужную ветку, вернуться из нее и командой git stash pop вернуться обратно, продолжив работу.
Просмотр разницы между текущим комитом и родительским комитом
```bash
git show
```
Для ссылки на родительский комит HEAD~ (HEAD~2, HEAD~3 …)
#### Метки
Метка дает имя комиту
Просмотр файлов в индексе
```bash
git ls-files
```
#### Слияния
Для слияния текущей ветки с указанной в параметре команды merge
```bash
git mege ветка
```
* Если сливаемая ветка является прямым потомком текущей ветки, то происходит сливание Fast Forward, при котором просто указатель текущей ветки (т.е. HEAD) просто начинает указывать (переносится) на сливаемую ветку (фактически)
* Если сливаемая ветка не является прямым потомком (например ветка master изменилась после того как от не отделилась сливаемая с ней ветка feature) ветки в которую она сливается произойдет трехходовое слияние

В результате такого слияния будет создан новый комит у которого 3 предка (общий комит предок для двух сливаемых веток и последние комиты каждой из сливаемых веток).
Если при таком слияние происходит конфликт, комит не произойдет.
Список файлов в которых имеются конфликты можно посмотреть командой (они будут помечены как unmerged)
```bash
git status
```
После устранения конфликтов изменения надо проиндексировать
```bash
git аdd .
```
и зафиксировать комит слияния:
```bash
git commit -m “сообщение”
```
При слиянии конфликт не происходит, если меняются разные файлы и/или меняются разные строки в одном файле.
#### Перенос (rebase)
Команда
```bash
git rebase <новая основа (ветка)>
```
позволяет создать линейную историю комитов при слиянии двух веток с общим предком.
При переносе прямым предком текущей ветки (допустим feature) становится ветка, указанная в качестве параметра команды rebase (допустим master). При этом в истории текущей ветки (feauture) создаются новые комиты, добавляющие все изменения текущей втеки от общего c новой базой (master) предка (этот предок по сути является старой базой текущей ветки).
После переноса базы можно слить ветку, ставшую новой базой (mater), с веткой (feature) способом Fast Forward (git merge feature).
При таком переносе история ветвления теряется, хотя результат будет тот же что и у трехходового способа слияния с помощью git merge.
Так как при rebase создается новая история комитов, воспроизводящих отличия текущей ветки от новой базы, то при каждом таком новом комите возможны ситуации конфликтов, после разрешения которых нужно продолжить reabase командой:
```bash
git rebase --continue
```
#### Удаленные (remote) репозитории
Для создания общего репозитория используется опция --bare (пустой). Имя такого репозитория по соглашению должно заканчиваться на .git:
```bash
git init --bare имя.git
```
Особенности общего (bare) репозитория
* репозиторий помещается в корне папке, а не в .git
* рабочая директория в таком репозитории отсутствует

Просмотр имен удаленных репозиториев
```bash
git remote
```
Добавление удаленного репозитория (будет изменен файл .git/config)

```bash
git remote add имя путь
```
Клонирование удаленного репозитория
```bash
git clone путь
```
Отправка изменений в удаленный репозиторий
* если ветка на удаленном репозитории не существует
```bash
git push репозиторий ветка
```
* если ветка существует
```bash
git push
```
Скачивание изменений из удаленного репозитория
```bash
git pull репозиторий ветка
```
Для создания новой ветки на общем репозитории ветка сначала создается в локальном, а затем выполняется
```bash
git push репозиторий новая ветка
```
Для удаления ветки на удаленном репозитории
```bash
git push репозиторий :ветка
```
Для того что бы находясь в какой-либо локальной ветке, не указывать <репозиторий> <ветка> каждый раз при выполнении команд push, pull надо настроить отслеживание (set-upstream)
находясь в локальной ветке, связываемой с удаленной веткой –
```bash
git branch -u <удаленный репозиторий>/<удаленная ветка>
```
Указать git отправлять на удаленный репозиторий только текущую ветку, а не все отслеживаемые можно следующей настройкой

```bash
git config --global push.default simple
```
При отслеживании удаленного репазитория в локальном репазитории создается соответствующая ветка типа репазиторий/ветка. Таким образом при работе с удаленным репазиторием существуют три синхронизирующееся ветки
* локальная
* отслеживаемая удаленная в локальном репазитории (репазиторий/ветка)
* удаленная

Посмотреть отслеживаемые удаленные в локальном репазитории ветки
```bash
git branch –r
```
Посмотреть информацию об удаленных ветках и их связях с локальными
```bash
git remote show репазиторий
```
Получить изменения (новые комиты) из удаленного репазитория можно двумя способами
* сначала командой fetch из удаленного репазитория обновляется локальная отслеживаемая удаленная ветка (репазиторий/ветка), а затем локальная ветка сливается (merge) с этой локальной веткой репазиторий/ветка
```bash
git fetch репазиторий
git merge репазиторий/ветка
```

* все действия из (1) могут выполниться одной командой pull:
```bash
git pull
```
То есть сливания всегда происходят в локальном репазитории, на удаленном репазитории комиты только публикуются и хранятся для копирования (клонирования, сливания в локальный репазиторий).
При этом когда находясь в локальной ветке используется команда pull, аргументы для соответствующих команд fetch и merge берутся из секции [branch “ветка”] файла настроек репазитория .git/config (из какого удаленного репазитария будут забираться изменения (fetch) и с какой удаленной веткой будут сливаться локальные изменения 
(merge)).
Если удаленный репазиторий был изменен другим пользователем (сделан комит отсутствующий в локальном репазитории), то push не позволит отправить комит из локального репазитория  в удаленный, до тех пор пока с удаленного не будет вытянут (pull) новый комит другого пользователя (пока изменения не будут слиты локально).
Если в локальном репазитори имеются незафиксрованые зменения, и выполняется git pull, то в локальном репазитори будет выполнено слияние изменений в создан комит.
#### Настройки
Настройки репазтория git находятся в файле .git/config
#### Hooks
В папке hooks размещаются пользовательские скрипты командной оболочки, которые выполняются до или после определенных команд (действий) git.
Это можно использовать для публикации приложений через git.
Так при git push в удаленном репазитории запустится скрипт hook/post-receive
В свою очередь в этом скрипте можно выполнять команду сheckout -f, которая скопирует в указанную папку (установленную как рабочую директорию опцией --work-tree) содержимое репазитория (так как скрипт в hooks использует команду git не из корня репазитория git, требуется указать используемые репазиторий опцией --git-dir)
```bash
git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
```
Для реализации такого сценария существует gem
```bash
git-deploy.
```
Установка, настройка и использование git-deploy
1) Этот gem устанавливается в ruby, а не Gemfile rails приложения.
```bash
gem install git-deploy
```
2) Настройка удаленного репазитория, при push в который будет развертываться приложение (то есть будут выполняться hooks скрипты):
```bash
git remote add production user@example.com:/apps/mynewapp
```
3) Непосредственно создание репазитория (в том числе и соответствующей папки, если она не существует) и hooks скриптов выполняется командой:
```bash
git deploy setup -r "production"
```
4) Установка и настройка скриптов в папке deploy удаленного репазитория
```bash
git deploy init
```
5) Развертывание приложения
```bash
git push production master
```
