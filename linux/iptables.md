## [Iptables](https://netfilter.org/)
#### Текущие правила
```bash
iptables -nL
```
Политики (правила) по умолчанию
```bash
iptables -S
```
Сброисть все парвила
```bash
iptables -F
```
#### Настройка правил
В файле

**/etc/network/iptables.up.rules**

задаем настройки netfilter (фактически команды iptables без указания самого iptables)

*filter # имя таблицы в которую добавляются правила, есть еще mangle и nat

задаем настройки netfilter (в примере разрешаем все исходящие соединения и входящий SSH трафик)
```
-P INPUT DROP
-P OUTPUT ACCEPT
-P FORWARD DROP
-A INPUT -i lo -j ACCEPT
-A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEP
-A INPUT -p tcp --dport 22 -j ACCEPT
COMMIT
```
После сохранения этого файла нужно протестировать настройки
```bash
iptables-apply
```
Правило желательно помечать комментариями
```
-m comment - -comment "Комментарий к правилу"
```
которые должны быть заключены в двойные кавычки и вставляться до опции -j

При необходимости текущие правила можно сохранить
```bash
iptables-save /etc/network/iptables.up.rules
```
