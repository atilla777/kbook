## Модели
### Генерирование моделей
#### Модель для связи двух моделей отношением многое ко многому
```bash
rails g model Модель3 модель1:references модель2:references
```
Пример
```bash
rails g model GroupMembers user:references group:references
```
### Работа с моделями
#### Обновление одного параметра
```ruby
Модель.update_attribute :поле, значение
```
#### Установление текущей даты и времени в атрибут
```ruby
Модель.тouch :поле
```
#### Создание нескольких объектов ActiveRecord (например Post belongs_to :users), связанных отношением много к одному с объектом ActiveRecord (например, User has_many :posts)
```ruby
user = User.find(1)
user.posts = [Post1, Post2, PostN]
```
### Callbacks
Использования callback следует избегать (плохой стиль программирования).

Если при сохранении объекта в callback он тут же будет использован (например, его ID будет передан в качестве параметра в фоновую задачу), то вместо after_save для сохранения этого объекта лучше использовать after_commit, так как правильный ID сохраняемого объекта может быть и не получен до того, как коммит завершится.

### ActiveRecord::Relation
При использовании методов, создающих Relation SQL запрос, выполняется только при использовании методов, требующих получения информации из результирующих записей запроса (ленивая загрузка).
Realtion позволяет вызывать цепочку методов, конструируя таким образом SQL запрос.
С результирующими записями, полученными в виде Relation необходимо работать как с массивами (то есть для обращения к одной записи использовать либо индекс элемента массива, либо методы типа first|last).
### Scope
Scope определяются в модели (по сути это определение методов класса модели) и позволяют создавать пользовательские методы для Relation, которые могут вызваться по цепочке.
```bash
scope :important, -> { where(:is_important => true) }.
```
Результат вызова Scope это Relation объект.

Для объектов Scope доступны методы модели, то есть сделав метод модели, к примеру – для фильтрации записей, этот метод можно по цепочке вызвать для Relation объекта
```ruby
Модель.scope1.scope2.метод_модели
```
Для использования в определении scope созданной в другой модели scope используется merge:
```ruby
scope :имя1, -> { joins(:device).merge(Device.имя2) }
```
Модель.where(nil) или Модель.all создают объект Relation, из которого можно строить сложные запросы, взывая методы по цепочке
```ruby
my_scope = Модель.all
my_scope = my_scope.where(type: :tag)
my_scope = my_scope.limit(10).to_a
```
или для создания новой записи
```ruby
my_scope = Модель.all
@ресурс = my_scope.build
@ресурс.attributes = my_params
@ресурс.save
```
### Concerns
Concerns (концерны) позволяют вынести одинаковый код из разных моделей или контроллеров в один concern файл. То есть в концерны выносится код, наделяющий общими свойствами модели или контроллеры, например, комментируемость модели (концерн Taggable), тегируемость модели), общие методы контроллеров (например, одинаковые фильтры при авторизации).
С помощью концернов можно
1) определить общие для нескольких моделей связи, валидаторы, scopes, коллбэки (см. комментарий 1 ниже);
2) определить общие для нескольких моделей методы экземпляров (см. комментарий 2 ниже);
3) определить общие для нескольких моделей связи и (см. комментарий 3 ниже);

Файлы с Concern помещаются в подпапку concerns папки модели или контроллера.

#### Как определить concerns
В класс модели добавить
```ruby
include Концерн
```
В файле с концерном
```ruby
module Commentable
  extend ActiveSupport::Concern
  included do
    has_many :comments, as: :commentable # 2 метод будет выполнены в контексте класса модели
  end
  def метод # 3 методы этого модуля будут методами экземпляров моделей
    …
  end
  module ClassMethods # 1 методы этого модуля будут методами классов моделей
    def метод
      …
    end
  end
end
```
