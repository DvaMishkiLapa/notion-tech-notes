# AsyncIO

## Ссылки

1. Введение в асинхронное программирование на Python (2020);

[Введение в асинхронное программирование на Python](https://habr.com/ru/company/otus/blog/509328/)

1. AsyncIO для практикующего python-разработчика (2017);

[AsyncIO для практикующего python-разработчика](https://habr.com/ru/post/337420/)

1. Что внутри asyncio (2019).

[Что внутри asyncio](https://habr.com/ru/post/453348/)

## Цикл событий

В цикле событий запускаются асинхронные задачи и обратные вызовы, запускаются
подпроцессы.

Основные методы:

- `asyncio.get_event_loop()` - получение текущего цикла событий. Если цикл событий
не был установлен, будет создан новый цикл событий, который установит цикл как
текущий.