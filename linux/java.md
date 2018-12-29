##### [Linux](index.md)
##### [Главная страница](../index.md)
### Java в Ubuntu Linux
Поиск доступных пакетов Java (ищутся OpenJDK версии)
```bash
sudo apt-cache search openjdk
```
Установка Java (в примере устанавливает 8я версия)
```
sudo apt-get install openjdk-8-jre
```
Список установленных пакетов Java и настройка того, какая Java будет исопльзоваться
```bash
sudo update-alternatives --config java
```



