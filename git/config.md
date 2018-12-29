##### [Git](index.md)
##### [Главная страница](../index.md)
## Настройка git
Настройки пользователя и его инструментов (редакторов для сравнения и исправления конфликтов)
```shell
git config --global user.name имя
git config --global user.email почта
git config --global diff.tool vimdiff
git config --global merge.tool vimdiff
```
Добавление удаленного репозитория - доступ к github по паролю
```shell
git remote add origin https://github.com/<пользователь>/<репозиторий>.git
```
Добавление удаленного репозитория - доступ к github по сертификату
```shell
git remote add origin git@github.com:<пользователь>/<репозиторий>.git
```
