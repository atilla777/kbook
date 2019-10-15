##### [Главная страница](../index.md)
##### [Sysmon](index.md)
## [Sysmon](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
### Основные команды
##### Установить sysmon (генерировать не только md5, но и sha256, протоколировать сетевые соединения, переименовать драйвер в **prnthp**)
```cmd
sysmon64 -accepteula -i -h md5,sha256 -d prnthp -n
```
#### Опции sysmon 
* -d - маскирует имя процесса, сервиса и драйвера sysmon (в примере выше имя sysmon становится prnthp)
* -n – регистрация сетевых соединений
* -c – обновление конфигурации, дамп конфигурации (когда нет других аргументов)
> по умолчанию установка sysmon с ключом **-i** задействует следующие возможности:
* Process create (SHA1)
* Process terminate
* Driver loaded
* File creation time changed
* RawAccessRead
* CreateRemoteThread
* Sysmon service state changed

#### Текущие настройки
```cmd
sysmon64.exe -c
```
#### Применить файл конфигурации
```cmd
sysmon64.exe –c файл.xml
```
### Создание файла конфигурации
#### Протоколировать все события определенного типа
```
<Тип onmatch="exclude"/>
```
Пример (протоколируются все сетевые соединения и создание процессов)
```xml
<Sysmon schemaversion="4.22">
	<EventFiltering>
		<NetworkConnect onmatch="exclude"/>
		<ProcessCreate onmatch="exclude">
	</EventFiltering>
</Sysmon>
```

#### Все события кроме отфильтрованных (**exclude**) по условию (**condition**)
```xml
<NetworkConnect onmatch="exclude">
<Image name="network iexplore" condition="contains">
iexplore.exe
</Image>
</NetworkConnect>
> В примере выше параметр **name** перед condition позволяет записать в протоколе событий имя конкретного условия

#### Задания для групп фильтрующих правил логические условия их комбинации - OR или AND (без **RuleGroup** правила комбинируются через **OR**) 
```xml
<EventFiltering>
    <RuleGroup name="group 1" groupRelation="and">
      <ProcessCreate onmatch="include">
        <Image condition="contains">timeout.exe</Image>
        <CommandLine condition="contains">100</CommandLine>
      </ProcessCreate>
    </RuleGroup>
    <RuleGroup groupRelation="or">
      <ProcessTerminate onmatch="include">
        <Image condition="contains">timeout.exe</Image>
        <Image condition="contains">ping.exe</Image>
      </ProcessTerminate>        
    </RuleGroup>
    <ImageLoad onmatch="include"/>
  </EventFiltering>
```