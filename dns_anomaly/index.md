## Выявление туннелей в DNS трафике
##### Обычный DNS запрос
![](ordinary_dns_request.png)

##### Обычный DNS ответ
![](ordinary_dns_response.png)

##### Признаки инициирования туннеля в пакете DNS запроса (на примере программы для построения туннелей ==[iodine](https://github.com/yarrick/iodine)==)
![](iodine_initial_request.png)

##### Пакет DNS запроса с признаками туннеля при передаче через него данных
![](iodine_data_transfer_request.png)

##### Пакет DNS запроса с признаками туннеля при передаче через него данных
![](iodine_data_transfer_response.png)
