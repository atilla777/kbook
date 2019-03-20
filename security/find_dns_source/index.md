##### [Главная страница](../../index.md)
##### [Информационная безопасность](../index.md)
## Выявление источника сетевого трафика на Windows компьютере
### Подготовка к сбору и анализу информации
#### Скачать и установить утилиту анализа сетевого трафика (снифер)
 [Wireshark](https://www.wireshark.org)
#### Запустить Wireshark
![](1.png)
#### Выбрать сетевой интерфейс трафик на котором будет собираться с помощью Wireshark
![](2.png)
#### Настроить Wireshark для отображения поля со значением порта источника сетевых пакетов
![](3.png)
![](4.png)
#### Скачать и разархивировать пакет утилит для анализа Windows
[Sysinternals Suite](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite)
#### Запустить утилиту записи и анализа системных событий Windows - Process Monitor
![](5.png)
#### Остановить сбор событий в Process Monitor, удалить собранные события
![](6.png)
#### Настроить фильтр Process Monitor для сбора системных событий, связанных с отправкой UDP и TCP пакетов
![](7.png)
![](8.png)
![](9.png)
![](10.png)
#### Проверить, что фильтруемые Process Monitor события, не будут сохраняться.
![](11.png)
> В случае, если осуществляется поиск источника DNS запросов, с большой долей вероятности будет обнаружено, что таким источником является процесс **svchost.exe** - служба MS Windows "DNS клиент".
Что бы определить какое именно приложение "попросило" эту службу разрешить DNS имя, перед началом сбора трафика и системных событий службу "DNS клиент" необходимо остановить (тогда приложение, скорее всего, само будет отправлять DNS запросы).
![](12.png)
#### Задать в Wireshark тип фильтруемого трафика
![](13.png)
### Сбор информации
#### Начать захват трафика
![](14.png)
#### Начать сбор событий в Process Monitor
![](15.png)
#### Завершить захват трафика и событий
![](16.png)
#### Сохранить собранный трафик и события
![](17.png)
![](18.png)
### Анализ собранных данных
#### Найти пакеты анализируемого трафика
![](19.png)
#### Определить порт, используемый приложением, которое является источником трафика
![](20.png)
и по номеру клиентского порта найти название и идентификатор процесса, посылающего пакеты на сервер
### Дополнительные утилиты
#### Информация об используемых (в настоящее время) в Windows сетевых портах и открывших их процессах
> Консоль CMD необходимо запускать от имени администратора
```cmd
netstat -anbo
```
#### Сбор информации за определенный период времени (до прерывания работы команды нажатием Ctr + C)
```cmd
>for /l %g in () do @( echo write & netstat -anbo >> result.txt )
```
#### В режиме реального времени сетевые коммуникации Windows можно наблюдать с помощью утилиты TCPView
[TCPView](https://technet.microsoft.com/ru-ru/sysinternals/tcpview.aspx)
#### Информацию об открытых процессом Windows сетевых портах можно получить (в реальном времени) с помощью утилиты Process Explorer
[Process Explorer](https://technet.microsoft.com/ru-ru/sysinternals/processexplorer.aspx)
#### Утилиты Microsoft, позволяющие установить то, какой процесс использует сетевой порт и является источником сетевого трафика
[Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226)
[Microsoft Network Monitor](https://www.microsoft.com/en-us/download/4865)