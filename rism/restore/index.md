##### [Главная страница](../../index.md)
##### [RISM](../index.md)
### Восстановление RISM из резервной копии
> Этапы восстановления 1-4 и 6 не надо выполнять, если восстановление базы данных происходит на рабочей виртуальной машине с RISM, а не на машине, поднятой из резервной копии образа VirtualBox (импортированная конфигурация VirtualBox).

1.	Импортировать в VirtualBox последнюю версию экспортированной конфигурации образа виртуальной машины RISM.
2.	Запустить импортированную виртуальную машину RISM.
3.	Войти в консоль (shell) запущенной машины (локально или через SSH на 127.0.0.1:2221).
4.	Перейти в папку с кодом RISM (что бы была возможность использовать команды средства развертывания приложения - Capistrano):
```bash
cd ~/develop/rism
```
5.	Остановить сервисы приложения:
```bash
sudo systemctl stop puma
sudo systemctl stop sidekiq
sudo systemctl stop postgresql
```
6.	Обновить приложение RISM до последней версии:
```bash
cap production deploy
```
7.	Удалить содержимое базы данных:
```bash
rm –R /var/lib/postgresql/10/main/*
```
8.	С помощью Windows утилиты WinSCP переместить на виртуальную машину (в папку /home/rism) архивы с резервной копией базы данных – файлы base.tar.gz и pg_wal.tar.gz.
9.	Разархивировать содержимое резервной копии в папку базы данных:
```bash
cd ~
tar -zxvf base.tar.gz -C /var/lib/postgresql/10/main
tar -zxvf pg_wal.tar.gz -C /var/lib/postgresql/10/main
```
10.	Удалить из восстановленных файлов файл recovery.conf
```bash
sudo rm var/lib/postgresql/10/main/ recovery.conf
```
11.	Запустить СУБД
```bash
sudo systemctl start postgresql
```
12.	Восстановить права доступа к файлам базы данных
```bash
sudo chown –R postgres:postgres /var/lib/postgresql/10/main/
sudo chmod 700 -R /var/lib/postgresql/10/main/
```
13.	Запустить остальные сервисы RISM
```bash
sudo systemctl start puma
sudo systemctl start sidekiq
```
