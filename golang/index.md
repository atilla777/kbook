##### [Главная страница](../index.md)
##### [Go](index.md)
## [Go](https://golang.org/)

### Структура кода
##### Простое приложение
package main // в приложении должен быть главный пакет
```go
import "fmt" // импорт используемых модулей (в них определены используемые функции)

// в приложении должна быть главная функция
func main() {
fmt.Println("Привет, В.!")
}
```
### Пакеты
Пакеты в go это исходные коды библиотек, используемых в приложении.
Сторонние пакеты (полученные из Интернета) размещаются (при выполнении **go get**) в папке src рабочего каталога, текущего проекта go.
##### Установка пакета
Команды необходимо запускать 
```
go get пакет
```
Пример
```go
go get github.com/cavaliercoder/grab
```
Импорт такого пакета
```go
"github.com/cavaliercoder/grab"
```
### Переменные
Переменные записываются в low CamelCase
```go
nameName
```
##### Объявление и присвоение переменной **:=**
```go
x := значение
```
> Тип переменой определяется автоматически по типу значения

##### Объявление и присвоение нескольких переменных
```go
x, y := значение1, значение2
```
### Константы
####  Объявление константы и присвоение ей значения
```go
const Константа тип = значение
```
##### Автоинкремент (iota)
```
const (
	a = iota + 1 // 1 (0 в iota + 1)
	_ // тут было бы 2
	b // b = 3
)
```
### Условные выражения
```go
if выражение {
	код
} else {
	код
}
```
### Обработка исключений
##### Вызов исключения
```go
panic значение
```
##### Перехват вызова исключения для его обработки
```go
defer 
```
### Протоколирование
#### Импорт необходимого пакета
```go
import "log"
```

#### Вывод сообщения в os.Stderror
```go
log.Println("сообщение")
```
##### Вывод сообщения в os.Stderror и завершение работы программы
```
log.Fatal("сообщение"))
```
### Прочее
##### Запуск внешней программы
```go
cmd := exec.Command(result1)
cmd.start()
```
Весь код приложения (включая библиотеки) компилируется в один файл программы - у такого исполняемого файла не внешних зависимостей
```go
"строка с \n"
`строка как есть`
'символ'
```
У объявленных, но не проинициализированных переменных есть значение по умолчанию

* числа - 0
* строка - ""
* булевые – false

### Типы данных
#### Строки
##### Интерполяция строки
```go
name := "Ivan"
str := "Hello %s !"
interpolated_str := fmt.Sprintf(str, name)
```
#### Слайсы
Длина слайса
```go
len(слайc)
```
Capacity (емкость) слайса - длина массива на котором лежит слайс.

Capacity всегда в 2 раза больше чем (про запас) его длина.
При добавлении в слайс создается новый массив.
```go
cap(слайс)
```
##### Объеденить слайсы
```go
append(слайс1,слайс2...)
```
##### Создать слайс заданной дины
```go
make([]тип, длина)
```
Пример (при добавлении в такой слайс элемента, он будет добавлен 11м)
```go
make([]int, 10)
```
##### Создать слайс заданной дины и емкости (за счет резервирования (расходования) памяти повышается производительность - не надо будет создавать новый массив при добавлении новых элементов сверх заданной длины слайса)
```go
make([]тип, длина, емкость)
```
Для операций с разными типами (включая константы) типы нужно приводить - вызывать функцию имя которой равно имени типа, к которому приводится значение
int(значение)
#### Структуры
##### Объявление типа **структура**
```go
type Структура struct {
	имя1 тип1
	имяN типN
}
```
##### Инициализация структуры
```go
var s Структура
```
или
```go
s := Структура{поле1: значение1, полеN: значениеN}
```
> Так как при аллокации памяти под данные структуры происходит выравнивание байт (для оптимизации скорости обращения к памяти в компьютере адрес данных должен быть кратным четному числу) по размеру самого большого типа данных и при этом память выделяется в порядке следования полей структуры , для избежания перерасхода пустых байт в структуре поля определяются от типов с наибольшим размером к наименьшим. Подробнее о этом [тут](https://medium.com/german-gorelkin/go-alignment-a359ff54f272)
```go
sruct Структура  {
	имя1 int64
	имя2 int
	имя3 bool
}
```
#### Методы структуры
Методы - это функции, связанные со структурами (их приемниками являются структуры)
```go
func (приёмник *Структура) метод(параметр тип) тип_результата {
	код с приёмником (структурой)
}
```
##### Объявление анонимной (без имени типа) структуры
```go
struct {
	имя1 тип1
	имяN типN
} {
	имя1 значение1
	имяN значениеN
}
```
Если типы и имена переменных двух структур совпадают, то при сравнении, присвоении и т. п. переменных с типами таких структур требуется явное приведение типов.
```go
var struct one {
	first int
}
var struct two {
	first int
}

a := one {
	i 1
}

b := two {
	i 2
}
a = one(b)
```
Если одна из таких переменных является структурой с анонимным типом, то компилятор сам выполнит неявное приведении типов.
```go
var struct one {
	first int
}
a := one {
	i 1
}

b := struct {
	i 2
}
a = b
```
> В писании структуры содержится описание полей данных.
А в описании интерфейса перечисляются только методы его реализующие.
### Функции и методы
##### Функция модуля (доступна только в этом модуле)
```
функция ...
```
##### Функция которая может вызываться из модуля
```go
Функция ...
```
###### Определение функции
```go
func функция(параметр Тип) тип_возвращаемого_значения {
	код
	return значение // возврат значения
}
```
##### Вызов функции
```go
функция(параметр)
```
##### Отложенный вызов функций
Вызванная с помощью defer функция будет выполнена последней текущей функции (функция внутри которой вызывается отложенная)
```go
defer функция()
```
> defer используется для:
* закрытия файлов (других объектов ресурсов, требующих закрытия) в конце выполнения текущей функции 
* перехвата и обработки исключений (defer функции вызываются даже если были исключения)
### Указатели
Указатель это способ совместного использования данных без их копирования и способ выйти за границы области видимости памяти функции.
#### Передача параметра в функцию по значению
```go
func функция(тип параметр) {
	код
}
```
####  Передача параметра в функцию по ссылке (указателю)
```go
func функция(тип *параметр) {
	код
}
переменная := значение
функция(&переменная)
```
>  & перед именем переменно говорит о том что будет взят адрес, а не значение переменной. * говорит о том что в параметре содержится указатель на адрес переменной и в функции будет использоваться значение параметра расположенное по данному адресу.

### Интерфейсы
Интерфейсы используются для реализации в go утиной типизации – если что-то крякает как утка (читай, реализует метод) то это и есть утка.

То есть имея (реализуя) несколько интерфейсов одна и таже структура может относится к нескольким утиным типам.

Или разные структуры могут реализовывать один интерфейс (иметь один утиный тип) и быть его приёмником.

Интерфейс реализован в структуре, если с ней связаны методы, перечисленные в интерфейсе и тип аргументов этих методов в структуре, совпадает с типами аргументов этих методов в перечислении в интерфейсе.
##### Определение интерфейса

После определения интерфейса

> Для производительности в функцию лучше определять не с аргументами, а с ссылками -**аргумент *Тип**. Это позволяет избежать копировании данных (читай – расход памяти) при передаче аргументов.

### Компиляция
##### Скомпилировать безоконное приложение
```go
go buil программа.go -ldflags -H=windowsgui
```
##### Уменьшить размер исполняемого файла за счет исключения отладочной информации
```go
-ldflags="-s -w"
```
##### Уменьшить размер исполняемого файла с помощью [UPX](https://upx.github.io/) за счет его сжатия (не рекомендуется для часто запускаемых файлов, так как перед запуском каждый раз выполняется декомпрессия)
```cmd
upx --brute приложение
```