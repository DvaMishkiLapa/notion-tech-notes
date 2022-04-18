# Итераторы и генераторы

## Ссылки

1. ****Итерируемый объект, итератор и генератор****

[Итерируемый объект, итератор и генератор](https://habr.com/ru/post/337314/)

1. ****Понимание итераторов в Python****

[Понимание итераторов в Python](https://habr.com/ru/post/488112/)

## Итераторы

***Итератор*** - интерфейс, предоставляющий доступ к элементам *коллекции* (это может быть массив или контейнер). Итертор только предоставляет доступ, не выполняя итерацию по ним.

***Итерируемый объект*** - объект, в котором есть метод `iter`. Итератор же - объект, в котором есть два метода: `iter` и `next`. Если есть объект `C` (список, например), то для получения итератора к нему, необходимо вызвать `iter(C)`.

Для получения элементов из итератора необходимо вызывать `next(A)`, где `A` - итератор.

У итератора, часто, нет длинны. Элементы могут генерироваться.

Некоторые итерируемые не являеются итераторами, но используют другие объекты как итераторы. Например `list`. В нем реализован `iter`, но не реализован `next`.

### Пример итераторов в Python

```python
class ListIterator(Iterator):
    def __init__(self, collection, cursor):
        """
        :param collection: список
        :param cursor: индекс с которого начнется перебор коллекции.
        так же должна быть проверка -1 >= cursor < len(collection)
        """
        super().__init__(collection, cursor)

    def first(self):
        """
        Начальное значение курсора -1.
        Так как в нашей реализации сначала необходимо вызвать next 
         который сдвинет курсор на 1.
        """
        self._cursor = -1

    def next(self):
        """
        Если курсор указывает на послений элемент, то вызываем StopIteration,
        иначе сдвигаем курсор на 1
        """
        if self._cursor + 1 >= len(self._collection):
            raise StopIteration()
        self._cursor += 1

    def current(self):
        """
        Возвращаяем текущий элемент
        """
        return self._collection[self._cursor]
```

```python
class ListCollection(Aggregate):
    def __init__(self, collection):
        self._collection = list(collection)

    def iterator(self):
        return ListIterator(self._collection, -1)
```

```python
collection = (1, 2, 5, 6, 8)
aggregate = ListCollection(collection)
itr = aggregate.iterator()

# обход коллекции
while True:
    try:
        itr.next()
    except StopIteration:
        break
    print(itr.current())
```

## Генераторы

***Генераторами*** называют итераторы, которые выглядят как определение функции, где вместо `return` есть `yield`. 

Любая функция в Python, в теле которой встречается ключевое слово `yield`, называется *генераторной функцией -* при вызове она возвращает *объект-генератор*.

*Объект-генератор* реализует интерфейс итератора, соответственно с этим объектом можно работать, как с любым другим *итерируемым объектом*.

### Пример генераторов в Python

```python
def fibonacci():
    prev, cur = 0, 1
    while True:
        yield prev
        prev, cur = cur, prev + cur

for i in fibonacci():
    print(i)
    if i > 100:
        break
```