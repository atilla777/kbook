##### [Главная страница](../index.md)
##### [Ruby](index.md)
## Объектно ориентированное проектирование
### Основные принципы
* [SOLID](https://ru.wikipedia.org/wiki/SOLID)

Single responsibility, Open-closed, Liskov substitution, Interface segregation и Dependency inversion
* [DRY](https://ru.wikipedia.org/wiki/Don%E2%80%99t_repeat_yourself)

Don’t repeat yourself
### Общие замечания
Приложение необходимо проектировать (писать) так, что бы в него легко было вносить изменения.
Далее по тексту дано описание общих принципов проектирования кода, позволяющих добиться этой цели.
### Разграничение ответственности
#### Единичная ответственность (single responsibility)
Каждый класс и каждый метод должны иметь только одну ответственность (обязанность).
Как проверить - описание того, что делает класс (метод) должно поместиться в одном предложении без слов "и" "или".

#### Не использовать имена переменных экземпляра
Внутри классов не стоит использовать имена переменных экземпляра, а использовать методы, возвращающие значения переменных.

#### Не использовать примитивные типы данных
Вместо примитивных типов данных лучше использовать обертки - объекты представляющие данные.
Например, вместо массива, где каждому индексу соответствует свойство использовать структуру, свойства которой ссылаются на индексы.
```ruby
class Класс
  def метод
    good_struct = GoodStruct.new(arr)
    good_struct.метод
  end

  GoodStruct = Struct.new(arr) do
    def поле1
      arr[0]
    end
    def поле2
      arr[1]
    end
  end
end
```
### Изоляция связей
#### Инъекция связи
Вместо использования имени одного класса внутри других классов следует использовать абстракцию - переменную (или метод) в которой содержится внешний класс.
Сам внешний класс передается при инициировании класса как параметр.
```ruby
class Класс
  attr_reader :external_klass
  def initialize(external_klass)
    @external_klass = external_klass
  end

  def метод
    external_klass.method
  end
end
```
#### Изоляция создания экземпляра внешнего класса
Если инъекция класса не реализуема, то надо изолировать создание экземпляра внешнего класса.
По сути надо стараться сконцентрировать весь код использующий зависимость от внешнего класса в одном месте кода, так его легче найти и, при необходимости, откорректировать.
```ruby
class Класс
  attr_reader :external_klass
  def initialize(external_klass)
    @external_klass = external_klass
  end

  def external_klass
    @external_klass ||= ВнешнийКласс.new
  end
end
```
#### Изоляция внешних сообщений
Принцип тут тот же, что и при изоляции создания экземпляра внешнего класса - вызовы методов внешних классов локализовать в отдельном месте кода (методах - обёртках).
```ruby
class Класс
  def метод
    оболочка.external_method
  end

  def оболочка
    external_klass.external_method
  end
end
```
#### Устранение зависимостей от порядка аргументов
Для этого можно использовать хэши (по сути используются именованые параметры).
```ruby
class Класс
  def initialize(хэш)
    @параметры = defaults.merge(хэш)
  end

  def defaults
    {параметр: 'значение по умолчанию'}
  end
end
```

> Если аргументы в используемом внешнем методе зависят от порядка их следования, то можно создать метод-обёртку, в котором уже использовать для параметров хэш.

### Управление направлением зависимостей.
При проектировании следует добиваться того, что бы классы зависели от классов (абстрактных), которые редко меняются.

### Поиск открытых интерфейсов
Определение сигналов, передаваемых между объектами.
Сигналы, посылаемые объекту, должны говорить ему что сделать, а не как.
### Упрощение контекста
У объектов должны быть простые контексты.
### [Закон Деметры](https://ru.wikipedia.org/wiki/%D0%97%D0%B0%D0%BA%D0%BE%D0%BD_%D0%94%D0%B5%D0%BC%D0%B5%D1%82%D1%80%D1%8B)
Закон Деметры (LoD) гласит - нельзя посылать сообщение одному объекту через другой объект, тип которого отличается от первого объекта (разговор возможен только с ближайшими соседями).

Другими словами - нельзя передавать сообщения по цепочке (аналогия: методы - это вагоны состава, если в его любом месте произошел сбой, раваливается весь состав).
```ruby
some_object1.some_object2.method
```
Не будет нарушением закона Деметра, если результат отправки сообщения в цепочке одно типа, например Enumerable
```ruby
some_hash.keys.each { |k| puts k}
```
### Неявная (утиная) типизация
Вместо выбора посылаемого метода на основе типа объекта
```ruby
case some_object
when String
  some_object.some_method1
when Integer
   some_object.some_method2
end
```
необходимо реализовать в объекте неявный тип, то есть заменить селектор класса на открытый интерфейс (метод) неявного типа.
### Наследование
#### Абстрактный класс
Абстрактный класс должен содержать в себе не изменяемую в потомках часть алгоритма.
Соответственно в реализации шаблонных методов в потомках должна содержаться изменяющаяся часть алгоритма.

Этот алгоритм определяется с помощью шаблонных методов.

В ходе проектирования лучше выделять из конкретных классов абстрактный класс, а не наоборот, так как забытая общая часть в конкретном классе не так страшна, как забытая конкретная часть в абстрактном.
#### Шаблонный метод
В родительский (абстрактный) класс вносится метод, конкретная реализация которого может быть переопределена в классах потомках.

Таким образом в абстрактном классе алгоритм задается с помощью абстрактных методов, возможно без детализации.

В абстрактном классе должны быть все шаблонные методы потомков.

Шаблонный метод в абстрактном классе должен возвращать ошибку NotImplementedError, что бы облегчить жизнь разработчику подкласса.
```ruby
class SomeAbstractClass
  def some_action # общий алгоритм некой операции
    template_method1
    template_method2
  end

  def template_method
    raise NotImplementedError, "subclass did not define #template_method"
  end
end

class SomeSubclass < SomeAbstractClass
  def template_method1
    # конкретная реализация метода
  end
  def template_method2
    # конкретная реализация метода
  end
end
```
#### Hook метод
Что бы не полагаться на вызов метода super в подклассах рекомендуется использовать hook методы.

Смысл таких методов в том, что используются они в родительском (абстрактном) классе, а определяются в дочернем (конкретном).
```ruby
class SomeAbstractClass
  def initialize
    some_hook_method
  end

  def som_hook_method; end
end

class SomeSubclass < SomeAbstractClass
  def some_hook_method
    # специфичная реализация метода, без необходимости вызова родительского метода (super)
  end
end
```
#### Миксины
В Ruby для наделения объектов сходным поведением используются миксины - включение модуля в классы, объекты которых необходимо наделить сходным поведением.
### Наследование и композиция
Объединять объекты в приложение можно с помощью наследования или композиции.
Выбор между наследованием и композицией обуславливается тем, что для решения задачи важнее - плюсы и минусы одного подхода или плюсы и минусы другого.
#### Наследование
* Плюс - сложная иерархия классов (связанность классов между собою)
* Минус - простая система обмена сообщениями между объектами (меньше надо знать куда какое сообщение посылать)
#### Композиция
* Плюс - не требуется выстраивать иерархию между классами
* Минус - более сложная система обмена сообщениями между объекатами (надо знать кому и какие сообщения посылать).
### Тестирование
Необходимо тестировать
* входящие сообщения - когда важно проверить состояние принимающего объекта
* исходящие сообщения - когда важно проверить действие (побочное) источника сообщения
### Литература
[Practical Object-Oriented Design in Ruby: An Agile Primer](https://www.oreilly.com/library/view/practical-object-oriented-design/9780132930895/)

> Название в переводе - Ruby. Объектно-ориентированное проектирование
