##### [Главная страница](../index.md)
##### [Прочее](index.md)
## [Redis](https://redis.io)
### Начало работы
#### Запуск сервера
```bash
redis-server
```
#### Запуск клиента
```bash
redis-cli
```
#### Проверка соединения с сервером
```bash
redis-cli
ping # результирующий вывод - pong
```
### Основные команды
#### Записать значение в ключ
```bash
SET ключ "значение"
```
#### Считать значение ключа
```bash
GET ключ
```
#### Удалить ключ и значеие
```bash
DEL ключ
```
#### Записать ключ и значение, если ключа еще нет в базе Redis (SET Not eXist)
```bash
SETNX ключ "значение"
```
#### Увеличть на 1 значение ключа
```bash
INCR ключ
```
> Использование INCR позволяет безопасно инкрементировать значение ключа при работе с ним нескольких Redis клиентов (данные будут непротиворечивыми).

#### Учтановить время жизни ключа м его значения (в секундах)
```bash
EXPIRE ключ секунды
```
> Если для ключа устанавливается новое значение через SET, то параметр TTL (время жизни) перестает быть заданным

#### Просмотреть оставшееся время жизни ключа
```bash
TTL ключ
```
> Значение TTL -2 означает, что ключ не существует, а значение -1, что ключ никогда не будет удален.

### Списки
Если значения ключа устанавливаются командами RPUSH, LPUSH, то в ключе будет храниться список.
#### Добавить элемент в конец списка (Right PUSH)
```bash
RPUSH ключ "элемент"
```
#### Добавить элемент в начало списка (Left PUSH)
```bash
LPUSH ключ "элемент"
```
#### Получить диапазон элементов списка
```bash
LRANGE ключ начало конец
```
> У первого элемента индекс 1 (параметр "начло"), а у последнего -1 (параметр "конец").

#### Получить значение ключа в котором находится список
```bash
LRANGE ключ 0 -1
```
> Командой GET это значение не получить.

#### Длина списка
```bash
LLEN ключ
```
#### Удалиить и получить первый элемент списка
```bash
LPOP ключ
```
#### Удалиить и получить последний элемент списка
```bash
RPOP ключ
```
### Множества (Set)
Множества - это неупорядоченные элементы, хранящиеся в ключе
#### Добавить в ключ элемент Set
```bash
SADD ключ "элемент"
```
#### Удалить из ключа элемент Set
```bash
SREM ключ "элемент"
#### Вывод всех значений ключа, в котором находится Set
```bash
SMEMBERS ключ
```
#### Проверка наличия значения в ключе Set
```bash
SISMEMBER ключ "значение" # 0 - элемента нет, 1 - есть
```
#### Объединения значений двух ключей Set в новый Set и вывод результата
```bash
SUNION ключ ключ
```
#### Сортированный Set
В сортированном Set у каждого элемента имеется связанный с ним уровень (score) - значение по которому выполняется сортировка.
#### Добывить в ключ сортированный Set
```bash
ZADD ключ уровень "элемент"
```
### Хэши
Хэши позволяют хранить объекты - связанные с ключем поля хэша и их значения.
#### Добавить в ключ (Хэш) значение поля хэша (добавить значение одного поля хэша)
```bash
HSET ключ:подключ поле "значение"
```
> Подключ позволяет отделить поля одного объекта в ключе от другого.

#### Добавить значения нескольких полей хэша
```bash
HMSET ключ:подключ поле "значение" поле2 "значение2"
```
#### Получить значение одного поля хэша
```bash
HGET ключ:подключ поле
```
#### Получить все значения полей объекта (хэша)
```bash
HGETALL ключ:подключ
```
#### Удалить значение одного поля хэша
```bash
HDEL ключ:подключ поле
```
#### Инкрементивароть на указанную велечину значение одного поля хэша
```bash
HINCRBY ключ:подключ поле велечина
```
### Каналы, подписки и публикации
Redes поддерживает следующие 3 типа сообщений:
* подписка (SUBSCRIBE)
* отписка (UNSUBSCRIBE)
* сообщение (MESSAGE) - "прилетает" подписчику при появлении сообщения при его публикации
#### Подписка на канал
```
SUBSCRIBE канал
```
#### Отписка от канала
```
UNSUBSCRIBE канал
```
#### Публикация в канал
```
PUBLISH канал "сообщение"
```
#### Подписка на канал по паттерну
```
PSUBSCRIBE канал*
```
> Подписка будет на все каналы название которых начинается на "канал".