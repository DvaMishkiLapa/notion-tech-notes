# Магические слова

## Ссылки

1. 5 ключевых понятий Python и их магические методы (2020).

[5 ключевых понятий Python и их магические методы](https://nuancesprog.ru/p/10529/)

## Смысл именования

Помимо традиционного именования методов и функций есть:

1. `__имя_функции` - применение метода должно быть ограничено внутри класса. Не
предполагается, что его будут вызывать как API;
2. `__имя_функции__`- магический метод.

## Магические методы в Python

### Инстанциирование `__new__` и `__init__`

- `__init__` - метод инициализации объекта класса. Объявление атрибутов, вызов
функций;
- `__new__` - метод, отвечающий за создание нового экземпляра класса, который
потом будет передан в `init`.

```python
class Product:
    def __new__(cls, *args):
        new_product = object.__new__(cls)
        print("Product __new__ gets called")
        return new_product

    def __init__(self, name, price):
        self.name = name
        self.price = price
        print("Product __init__ gets called")
```

### `__repr__` и `__str__`

- `__repr__` - показывает информацию о инстанировании, можно использовать в `eval()`;
- `__str__` - метод, отвечающий за строковую интерпретацию объекта, например, через
`print()`.

```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def __repr__(self):
        return f"Product({self.name!r}, {self.price!r})"

    def __str__(self):
        return f"Product: {self.name}, ${self.price:.2f}"
```

### Контекстный менеджер `__enter__` and `__exit__`

Связано с конструкцией `with`. Срабатывают при выходе и выходе из контекстного
менеджера.

- `__enter__` - выполняется при входе в контекстный менеджер;
- `__exit__`- выполняется при выходе из контекстного менеджера.

```python

class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def __str__(self):
        return f"Product: {self.name}, ${self.price:.2f}"

    def _move_to_center(self):
        print(f"The product ({self}) occupies the center exhibit spot.")

    def _move_to_side(self):
        print(f"Move {self} back.")

    def __enter__(self):
        print("__enter__ is called")
        self._move_to_center()

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("__exit__ is called")
        self._move_to_side()

product = Product("BMW Car", 50000)
with product:
    print("It's a very good car.")
```

### Итерация через `__iter__` и `__next__`

- `__iter__` - магический метод возвращения итератора;
- `__next__`- магический метод выдачи элемента элементов из итерируемого,
находящегося в итераторе.

```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price

    def __str__(self):
        return f"Product: {self.name}, ${self.price:.2f}"

    def __iter__(self):
        self._free_samples = [Product(self.name, 0) for _ in range(3)]
        print("Iterator of the product is created.")
        return self

    def __next__(self):
        if self._free_samples:
            return self._free_samples.pop()
        else:
            raise StopIteration("All free samples have been dispensed.")

product = Product("Perfume", 5.0)
for i, sample in enumerate(product, 1):
    print(f"Dispense the next sample #{i}: {sample}")
```

### Контроль доступа к атрибутам: `__getattr__` and `__setattr__`

Методы вызываются во время работы с атрибутами объекта.

- `__getattr__` - выполняется при получении атрибута;
- `__setattr__`- выполняется при изменении атрибута в объекте.

```python
class Product:
    def __init__(self, name):
        self.name = name

    def __getattr__(self, item):
        if item == "formatted_name":
            print(f"__getattr__ is called for {item}")
            formatted = self.name.capitalize()
            setattr(self, "formatted_name", formatted)
            return formatted
        else:
            raise AttributeError(f"no attribute of {item}")

    def __setattr__(self, key, value):
        print(f"__setattr__ is called for {key!r}: {value!r}")
        super().__setattr__(key, value)
```