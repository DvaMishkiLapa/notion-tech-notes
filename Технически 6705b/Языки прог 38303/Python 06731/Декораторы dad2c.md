# Декораторы

## Ссылки

1. О декораторах в Python (2019);

[О декораторах в Python](https://habr.com/ru/company/otus/blog/460931/)

1. Декораторы Python: хватит это терпеть (2021);

[Декораторы Python: хватит это терпеть](https://habr.com/ru/post/560572/)

1. Сказка про декораторы в Python (2020).

[Сказка про декораторы в Python](https://habr.com/ru/post/524052/)

## Что такое декоратор?

***Замыкание (clouser)***, которое принимает функцию в качестве параметра и возвращает функцию, называют декоратором.

***Замыкание*** - функция, находящаяся внутри функции, и ссылается на переменные, объявленные во внешней функции.

Важно помнить, что декоратор выполняется только один раз при объявлении оборачиваемой функции. При дальнейшем вызове функции будет выполняться только внутренняя функция.

## Что можно предавать в декоратор?

В декоратор можно передавать как функции (когда функция в функции), либо аргументы могут быть переданы непосредственно в декоратор (`@dec(arg='kek)`).

Так же в декоратор можно передавать `callable` объекты (у которых есть **`call`**). У декорируемой функции могут быть аргументы. Для их передачи во вложенную функцию декоратора можно передать во внутреннюю функцию `args` (`In_deg_func(*args)`).

Обычно, декоратор возвращает функцию. Но вернуть можно что угодно, только его не получится вызвать.

## Примеры декоратора

### Пример простого декоратора

```python
def outer(some_func):
    def inner():
        print("before some_func")
        ret = some_func()  # 1
        return ret + 1
    return inner

def foo():
    return 1

decorated = outer(foo)  # 2
print(decorated())
```

```python
def outer(some_func):
    def inner():
        print("before some_func")
        ret = some_func()  # 1
        return ret + 1
    return inner

@outer
def foo():
    return 1

print(foo())
```

### Пример декоратора с параметром

```python
def fictive(decorator_text):
    def decorator_function(wrapped_func):

        def wrapper(*args):
            print(decorator_text, end='')
            wrapped_func(*args)
        return wrapper

    return decorator_function

@fictive(decorator_text='Hello, ')
def hello_world(text):
    print(text)

hello_world('world!')
```

## Примеры существующих декораторов в Python

### @classmethod

Декоратор `@classmethod` может быть вызван при помощи экземпляра класса, или напрямую, через собственный класс Python в качестве первого аргумента.

В соответствии с документацией Python: *он может быть вызван как в классе (например, `C.f()`), или в экземпляре (например, `C().f()`). Экземпляр игнорируется, за исключением его класса. Если метод класса вызван для выведенного класса, то объект выведенного класса передается в качестве подразумеваемого первого аргумента.*

### @staticmethod

Декоратор `@staticmethod` - это просто функция внутри класса. Вы можете вызывать их обоих как с инициализацией класса, так и без создания экземпляра класса.

Обычно это применяется в тех случаях, когда у вас есть функция, которая, по вашему убеждению, имеет связь с классом. 

### @property

Python содержит очень удобный небольшой концепт, под названием `property`, который выполняет несколько полезных задач:

- Конвертация метода класс в атрибуты только для чтения:
    
    ```python
    class Person(object):
    
        def __init__(self, first_name, last_name):
            """Конструктор"""
            self.first_name = first_name
            self.last_name = last_name
    
        @property
        def full_name(self):
            """
            Возвращаем полное имя
            """
            return "%s %s" % (self.first_name, self.last_name)
    ```
    
- Как реализовать сеттеры и геттеры в атрибут:
    
    ```python
    from decimal import Decimal
    
    class Fees(object):
    
        def __init__(self):
            """Конструктор"""
            self._fee = None
    
        @property
        def fee(self):
            """
            Возвращаем текущую комиссию - геттер
            """
            return self._fee
    
        @fee.setter
        def fee(self, value):
            """
            Устанавливаем размер комиссии - сеттер
            """
            if isinstance(value, str):
                self._fee = Decimal(value)
            elif isinstance(value, Decimal):
                self._fee = value
    
    if __name__ == "__main__":
        f = Fees()
    ```
    

Один из самых простых способов использования `property`, это использовать его в качестве ***декоратора метода***. Это позволит превратить метод класса в ***атрибут класса***.