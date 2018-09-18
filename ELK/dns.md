## Пассивный DNS
### Установка ELK
#### Установка Elasticsearch
Установить Elasticsearch согласно (официальной документации)[https://www.elastic.co/guide/en/elasticsearch/reference/6.4/deb.html]:
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
sudo apt-get update && sudo apt-get install elasticsearch
```
Статус Elasticsearch
```bash
sudo systemctl status elasticsearch
```
Запуск Elasticsearch
```bash
sudo systemctl start elasticsearch
```
Запуск Elasticsearch при загрузке ОС
```bash
sudo systemctl enable elasticsearch
```
Мониторинг запуска Elasticsearch в журнале syslog
```bash
sudo journalctl -f
```
Запрос к Elasticsearch (проверка работоспособности)
```bash
curl -X  GET localhost:9200
```
Настройка Elasticsearch производится в файле:
**/etc/elasticsearch/elasticsearch.yaml**
#### Установка Kibana
Установка производится согласно (официальной документации)[https://www.elastic.co/guide/en/kibana/current/deb.html]
Если Elasticsearch уже установлен, установка выглядит следующим образом:
```bash
sudo apt-get install kibana
```
Статус Kibana
```bash
sudo systemctl status kibana
```
Запуск Kibana
```bash
sudo systemctl start kibana
```
Запуск Kibana при загрузке ОС
```bash
sudo systemctl enable kibana
```
Запрос к Kibana (проверка работоспособности) - перейти по ссылке:
**http://localhost:5601**
Настройка Kibana производится в файле:
**/etc/kibana/kibana.yaml**
### Установка Packetbeat
Установка производится согласно (официальной документации)[https://www.elastic.co/guide/en/beats/packetbeat/current/packetbeat-installation.html]
Выглядит это следующим образом:
```bash
sudo apt-get install libpcap0.8
curl -L -O https://artifacts.elastic.co/downloads/beats/packetbeat/packetbeat-6.4.0-amd64.deb
sudo dpkg -i packetbeat-6.4.0-amd64.deb
```
Статус Packetbeat
```bash
sudo systemctl status packetbeat
```
Запуск Packetbeat
```bash
sudo systemctl start packetbeat
```
Запуск Packetbeat при загрузке ОС
```bash
sudo systemctl enable packetbeat
```
Установка Packetbeat Dashboard для Kibana
```bash
sudo packetbeat setup --dashboards
```
Настройка Packetbeat производится в файле
**/etc/packetbeat/packetbeat.yaml**
Значание параметров по умолчанию находятс яв той же папке в файле
**packetbeat.reference.yml**
Для сбора информации о DNS трафике этот файл должен выглядить следующим образом

packetbeat.interfaces.device: any
packetbeat.protocols:
- type: dns
  ports: [53]
  include_authorities: true
  include_additionals: true
output.elasticsearch:
  hosts: ["localhost:9200"]
logging.to_files: true
  
Для того, что бы убедиться в том, что связка Packetbeat - Elasticsearch работает (информация о сетевых пакетах собирается и индексируется) можно сделать запрос к Elasticsearch - найти все документы (выведены будут первые 10)
Для этого вначале надо узнать название индекса Elasticsearch в котором хранится информация от Packetbeat
```bash
curl 'localhost:9200/_cat/indices?v'
```
Зная имя индекса (можно использовать символ * в качестве замены любых символов в имени индекса)
```bash
curl 'localhost:9200/packetbeat-6.4.0-2018.09.17/_search?q=*&pretty'
```
Поиск DNS запроса по IP адресу:
```bash
curl -XPOST -H 'Content-Type: application/json' 'localhost:9200/packetbeat*/_search' -d'
{
  "query": {
    "bool": {
      "must": [
        {"term": {"client_ip": "192.168.43.44"}},
        {"term": {"type": "dns"}}
       ]
      }
    }
  }
}'
