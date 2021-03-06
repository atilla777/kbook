##### [Главная страница](../../index.md)
##### [Информационная безопасность](../index.md)
## Исследование исполняемых файлов на предмет наличия вредоносного кода
#### Типы методов исследования файлов
* Базовый статический анализ – получения общей информации о файле (название, размер, хэш-суммы и т.п.)
* Углубленный статический анализ – получение сведений о внутренностях файла (дизассемблирование и исследование кода)
* Базовый динамический анализ
* Углубленный динамический анализ
### Этапы исследования файлов
#### 1 - Сбор основной информации о файле
На данном этапе необходимо получить следующие сведения:
* название
* размер
* хэш-сумму
* используется упаковщик или нет
* содержащиеся в коде строки
* тип компоновки библиотек
##### хэш-сумма
Получение хэш суммы (алгоритм SHA1)
```CMD
certutil -hashfile файл.exe
```
Также можно воспользоваться утилитой с графическим интерфейсом [WinMD5](http://winmd5.com/).
##### Использование упаковщика
Файл упаковывается, чтобы уменьшить его размер и скрыть содержимое.
Признаком упакованного файла является то, что при его изучении в нем нет осмысленных строк и имеется небольшое количества названий функций (менее 10).
Обнаружение информации об использовании упаковщика можно выполнить с помощью графической утилиты [PEiD](http://www.softpedia.com/get/Programming/Packers-Crypters-Protectors/PEiD-updated.shtml).
Для исследования запакованного файла его надо распаковать. Сделать это можно с помощью утилиты [upx]( https://upx.github.io/) (если в качестве упаковщика был использован UPX).
```CMD
upx -d exe файл.exe
```
##### Строки

Поиск строк в коде файла с помощью утилиты [strings]( https://docs.microsoft.com/en-us/sysinternals/downloads/strings)
```CMD
strings.exe файл.exe
```
В строках могут содержаться доменные имена, URL, выводимые программой сообщения, имена используемых функций и библиотек.
##### Определение типа компоновки библиотек, используемых приложением
Типы компоновки:
* Статическая - библиотеки помещаются в коде приложения
* Динамическая - библиотеки подключаются после запуска программы
* На этапе выполнения - библиотеки подключаются в ходе выполнения программы, когда они требуются (часто используется во вредоносном ПО)

Для использования библиотек, вызываемых на этапе выполнения, используются функции:
* LoadLibrary
* GetProcAddress
* LdrGetProcAddress
* LdrLoadDll

> PE файл (Portable Executable) - в Windows это основной формат исполняемых файлов и динамически связываемых (компонуемых) библиотек (DLL).
Информация о всех загружаемых (импортируемых), динамически компонуемой программой, библиотеках и функциях хранится в PE заголовке.
Для просмотра заголовков файлов можно использовать утилиту [PEView]( http://wjradburn.com/software/).
Также в этом заголовке есть информация об экспортируемых программой или библиотекой функциях.
В свою очередь информация об используемых программой библиотеках и функциях позволяет определить назначение и возможности программы.
Получение списка используемых программой динамически подключаемых (импортируемых)  библиотек можно осуществить с помощью утилиты [Dependency Walker](http://www.dependencywalker.com/).
Перечень импортируемых библиотек, на которые стоит обратить внимание:
* Kernel32.dll - управление процессами и файлами
* Advapi32.dll - графический интерфейс
* User32.dll - графический интерфейс
* GDI32.dll - графический интерфейс

Перечень импортируемых функций, на которые стоит обратить внимание:
* CreateProcessA - создание новых процессов
* URLDownloadtoFile - скачивание файла по URL
* FindFirstFile FindNextFile - обход каталогов и обзор их содержимого
* SetWindowsHookEx - восприятие ввода с клавиатуры (используют кейлоегры)

#### Применяемые утилиты
* **certutil**
* [WinMD5](http://winmd5.com/)


