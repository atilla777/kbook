## [Logstash](https://www.elastic.co/products/logstash) ### Общие сведения

### Установка
При установке на linux сначала должна быть установлена подходящая вресия Java RE
```bash
sudo apt-get install openjdk-8-jre
```
Установка выполняется согласно [официальной документации](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html).
> Если Logstash был установлен с неправильной версией Java, его надо удалить (см. команду ниже), затем поставить нужную версию Java (см. пример выше), потом снова установить Logstash.
> Если Logstash был установлен с неправильной версией Java, его надо удалить (см. команду ниже), затем поставить нужную версию Java (см. пример выше), потом снова установить Logstash.
sudo apt-get install logstash
```
### Настройка
Запуск logstash
```bash
logstash agent –f конфигурация
```
Канал обработки данных (pipeline) logstash задается в конфигурационном файле и включает в себя следующие секции

* input – определяет источники данных (событий)
* filter (необязательный) – определяет правила нормализации данных, то есть как входные данные будут преобразованы в выходные данные
* output – определяет приемник выходных данных

Таким образом, проходящие через logstash данные обрабатываются в следующем порядке

источник событий -> кодек -> logstash{input->filter->output} -> кодек-> приемник

К каждому событию, обрабатываемому logstash он добавляет текущую дату и имя компьютера (агента logstash).

К каждому обрабатываемому событию logstash добавляет поля:

* @version -

* @timestamp - текущая дата.

* message - само, обрабатываемое logstash, событие (запись в обрабатываемом файле).

Каждая секция может содержать комбинацию используемых плагинов logstash.

Параметрами плагина (кодека) могут быть

* 1 - число
* "строка" - строка
* [] - массив
* {} - хэш (элементы на разных строках запятыми не отделяется)

Для ссылок на поля события

[поле]

Для вложенного поля
[поле] [вложенное поле]

Поле **@metadata** позволяет хранить вложенные поля, которые не попадут на вывод logstash, а используются для определения логики работы плагинов, например можно добавить поле test
```
mutate { add_field => { "[@metadata][test]" => "Hello" } }
```
Конфигурация logstash поддерживает условные выражения:
```
if [поле] == [поле] {

  …

} else if {

  …

} else {

  …

}
```
Для преобразования форматов входных или выходных данных используются кодеки.

Формат файла конфигурации (pipeline)
```
# комментарий

input {
  кодеки
  плагины
}
filter {
  плагины
}
output {
  кодеки
  плагины
}
```
#### Настройка input
Основные кодеки
* json
* multilane – преобразование многострочных записей событий в однострочные

Использование кодека
```
input {
  stdin {
    codec => multiline {
      pattern => "<паттерн признака многострочного события>"
      negate => "true" or "false"
      what => "previous или next"
    }
  }
}
```

Где
* pattern – паттерн регулярного выражения, совпадение с которым используется для определения того, что запись (событие) является многострочной
* negate – определяет относить запись к многострочной при совпадении c pattern (значение false) или нет (значение true)
* what – определяет что совпавшая с pattern запись является частью предыдущей записи (значение previous) или к следующей (next)

Основные плагины

* file – сбор из текстового файла
* syslog – сбор событий syslog по порту 514
* eventlog – сбор событий из файла событий Windows
* filebeat – сбор событий по порту 5403 (с шифрованием) от клиента filebeat (клиенты, передающие файлы журналов с удаленных хостов).

Использование в качестве источника данных файлов (плагин file)
```
file {
  path => "путь"
  start_position => beginning
  type => "тип события"
  sincedb_path => "путь"
}
file {
  …
```
Опция **path => "путь"** указывает путь к файлу (или файлам, в случае если происходит их ротация) который будет считываться logstash.

Опция **start_position => beginning** указывает logstash считывать файл сначала, а не с текущей обновленной позиции (если в файл продолжает записываться информация). По умолчанию (без этой опции) будут считываться только вновь добавленные в файл строки (аналог tail –f0).

Для того, что бы при перезапуске logstash не потерять информацию о последней считанной из файла строке logstash ведет базу sinecdb.

Опция **sincedb_path => 'путь'** указывает путь к этому файлу.

Опция **type => 'тип'** добавляет к событию поле, имя которого может использоваться для обработки события далее в filter.

#### Настройка filter

Основные плагины
* grok - фильтрация неструктурированных текстовых файлов (человекочитаемых логов)
* date - замена значений поля @timestamp
* mutate - изменение полей записи
* drop - отброс записи
* geoip - добавление геоинформации

#### Фильтр grok
Grok плагин с помощью предопределённых паттернов разбивает событие на именованные поля, получая на выходе json формат. К этим полям могут обращаться другие плагины, например geoip.
```
grok {
  patterns_dir => "./patterns"
  match => {
  "message" => “%{патерн:имя} %{патерн:имя}"
  }
}

```
Синтаксис фильтра grok
```
%{паттерн:имя}
```
где

* Паттерн – имя паттерна, представляющего собою регулярное выражение, сопоставление которого будет перехватываться logstash.
* Имя – имя поля соответствующей записи в формате json, которое будет содержать перехваченные паттерном данные.

Паттерны можно определить в файле patterns/extra (путь к патерам задается директивой patterns_dir) в виде
```
ПАТТЕРН регулярное выражение
```
Пример
```
NUMBER \d+
```
#### Регулярные выражения grok
Регулярные выражения grok используют синтаксис Oniguruma.

Для помещения найденного с помощью паттерна значения в поле используется следующая конструкция:
```
(?<поле>патерн)
```
Исключить паттерн из результата
```
(?:паттерн)
```
Любой символ
```
.
```
Символ из группы

```
[символы]
```
Экранирование символа
```
\
```
Квантификаторы (указывают количество символов)

Ноль или один
```
?
```
Один или более
```
+
```
Ноль или более
```
*
```
Диапазон количества символов
```
{от, до}
```
Якоря (указывает расположение символов)

Начало строки
```
^
```
Конец строки

```
$

```
В файле с определением паттернов при определении регулярного выражения можно использовать ранее определенные паттерны

Предопределнные паттерны можно найти по [ссылке](https://github.com/elastic/logstash/blob/v1.4.2/patterns/grok-patterns) или поиском в Google - **grok default patterns **


```
ПАТТЕРН %{ПАТТЕРН1} %{ПАТТЕРН2} %{ПАТТЕРН3}
```
Тут же можно определить какие поля будут добавлены в событие после обработки паттерна в logstash
```
ПАТТЕРН %{ПАТТЕРН1:поле} %{ПАТТЕРН2:поле}
```
То есть примирение паттерна в конфигурационном файле grok, при совпадении события (строки) с паттерном создаст определенные в паттерне поля (значения свойств JSON)
```
grok { match => {"mesasge" => "{ ПАТТЕРН }" } }
```
При конструировании регулярных выражений целесообразно использовать ресурс http://grokconstructor.appspot.com.

и (или) [вот этим сайтом](https://grokdebug.herokuapp.com/)

В случае ошибки в паттернах, из-за которой logstash не сможет разобрать строку журнального файла, информацию об этом можно увидить в переданном в Elasticsearch от Logstash JSON, в котором будет поле **_grokparsefailure**.

#### Фильтр Multilane
Multilane – плагин фильтрации многострочных логов (в rails многострочные логи).

#### Фильтр mutate
mutate
add_field
remove_field
convert

#### Плагин date – замена проставляемого logstash значения поля @timestamp на значение пользовательского поля (например ранее созданного с помощью gork)
```
date {
  match => [“поле”, “формат”]
}
```
где
* поле это пользовательское поле с датой,
* формат это Joda DateTimeFormat формат искомой в пользовательском поле даты
* time_zone – временная зона в формате Joda (необходима для преобразования времени в местное)

#### Настройка output
Основные плагины
* elasticsearch
* file

Вывод на консоль
```
stdout {}
```
Передача на elasticsearch (по HTTP):
```
elasticsearch {
}
```
Для указания адреса и порта хоста с elasticsearch
```
hosts => "хост:порт".
```
Индекс elasticsearch в который logstash передает дынные (документ) по умолчанию имеет вид – logstash-дата, то есть часть «дата» имени индекса определяется полем @timestamp события.

Если название индекса переопределить (опция плагина index => 'индекс'), то в elasticsearch станут недоступны, автоматически создаваемые в logstash поля типа поле.raw.

Эти поля не анализируются (not-analyzed) в elasticsearch, то есть их содержимое будет представлять собой один целый терм (то есть "foo bar" останется самим собой, а не превратится в "foo" и "bar").

Вывод в файл:
```
file {
  path => 'out_file'
}
```