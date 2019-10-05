##### [Главная страница](../../index.md)
##### [Информационная безопасность](../index.md)
## Исследование памяти с помощью volatility framework
### Установка
### Установка на Windows
Если, к примеру, установить готовый пакет chocolatey, то в нём может не быть профилей последний версий Windows. Поэтому лучше выполнить установку следующим образом. 
#### Установить python 2.7
Установить python можно через [chocolatey](https://chocolatey.org/packages?q=powershell) или через [обычный инсталятор](https://www.python.org/downloads/windows/).
#### Установить зависимости
* Distorm
В папке[релизы](https://github.com/gdabah/distorm/releases) необходимо найти полсденю версию для python 2.7 по ссылке **Python 2/3**. Например для версии 3.3.4 [distorm3-3.3.4.win-amd64.exe](https://github.com/gdabah/distorm/releases/tag/v3.3.4).
* PyCrypto
С этой [страницы](http://www.voidspace.org.uk/python/modules.shtml#pycrypto) необходимо скачать файл PyCrypto 2.6 for Python 2.7 64bit.
* openpyxl
```cmd
pip install openpyxl
```
* ujson 
```cmd
pip install ujson 
```
* yara
Скачать и установить последнюю версию yara для python 2.7 согласно инструкции на [странице](https://dfironthemountain.wordpress.com/2018/10/29/installing-volatility-on-windows/).
#### Установить последнюю версию volatility
```cmd
git clone https://github.com/volatilityfoundation/volatility.git
cd volatility
python.exe setyp.py
```
### Создание дампа памяти для исследования с помощью volatility
#### Dumpit
Дамп проще всего создать с помощью программы **dumpit** из пакета ****.
Скачать этот пакет можно с следующей страницы [comae](https://my.comae.com/login)(требуется регистрация).

Для создания дампа памяти достаточно запустить файл dumpite.exe.

Дамп сохранится в текущей директории.
> На диске должно быть столько свободного места, какой размер ОЗУ.

Для указания профиля ОС в volatility, нужно узнать версию исследуемой ОС и ее билд.
```cmd
winver
```

Эту информацию можно записать в имени файла дампа.
####
### Подбор профиля ОС для volatility
Перед началом работы нужно подобрать подходящий профиль.

Перечень поддерживаемых volatility профилей
```cmd
python.exe vol.py --info
```
> Профиль это запись вида **Win10x64_17763**.

В списке необходимо найти подходящий профиль и проверить что он работает.
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль pstree
```
Можно перебирать разные профили, пока не найдется подходящий и в выводе плагина pstree (команда выше) будут отображаться корректные названия процессов.
### Исследование вредоносного кода
В общем случае с помощью volatility следует выполнить:
* получить перечень процессов
* получить перечень сервисов
* получить перечень сетевых соединений
* получить перечень последних консольных команд
* найти вредоносный код, выгрузить его на диск и исследовать, в том числе в песочнице
#### Подсказка по командам  (плагинам) доступна следующим образом
```cmd
python.exe vol.py -h
```
#### Перечень процессов
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль pstree
```
#### Перечень сервисов
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль svcscan
```
#### Перечень сетевых соединений
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль netscan
```
#### Сохранить процесс в исполняемый файл
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль procdump --dump-dir ./output –p 868
```
> Для разных версий Windows для получения этой информации надо использовать разные плагины - см. справку по командам.
#### Перечень последних консольных команд (история команд)
```cmd
python.exe vol.py -f дамп.dmp --profile=профиль cmdscan
```
#### Полезные подсказки от [SANS](https://digital-forensics.sans.org/media/volatility-memory-forensics-cheat-sheet.pdf)
