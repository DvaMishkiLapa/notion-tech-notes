# RabbitMQ, AMQP, RPC

## Ссылки

1. RabbitMQ. Часть 1. Introduction. Erlang, AMQP (2020);

[RabbitMQ. Часть 1. Introduction. Erlang, AMQP](https://habr.com/ru/post/488654/)

1. RabbitMQ против Kafka: два разных подхода к обмену сообщениями (2018).

[RabbitMQ против Kafka: два разных подхода к обмену сообщениями](https://habr.com/ru/company/itsumma/blog/416629/)

## Что такое AMQP?

***AMQP*** - открытый протокол для передачи сообщений между компонентами системы. Основные компоненты системы могут обмениваться данными через ***AMQP-брокер***, который осуществляет доставку, может гарантировать эту доставку (возможно), распределяет потоки данных, занимается работой подписки на нужный тип сообщений.

***Exchange*** - точка обмена, в нее отправляют сообщения. Распределяет сообщения на одну или несколько очередей.

***Queue*** - очередь, структура данных, которая хранит ссылку на сообщение и отдает копии потребителям. Одна очередь может использоваться несколькими потребителями.

***Billing*** - правила, которое сообщает точке обмена в какую из очередей эти сообщения должны попадать.

![6f47e92a8d4ebca0e828abd0970596e9.jpg](RabbitMQ,%20AMQP,%20RPC%206cfb8a8d805f43ebaafa9e9cbf3eead6/6f47e92a8d4ebca0e828abd0970596e9.jpg)

## RabbitMQ

***RabbitMQ*** - брокер сообщений с открытым исходным кодом, придерживается принципов AMQP.

Основная идея модели обмена сообщениями - ***producer*** (издатель) не отправляет сообщения непосредственно в очередь. Издатель часто не знает, будет ли сообщение доставлено в принципе в какую-либо очередь. Вместо этого издатель отправляет сообщение только на обмен, где сам обмен знает, что с ним делать.

### Принцип работы RabbitMQ

1. Издатель отправляет сообщение в обменник (***exchange***);
2. ***Exchange***, получив сообщение маршрутизирует его в одну или несколько очередей
(***queue***);
3. ***Queue*** хранит ссылку на сообщение;
4. Как только *consumer* готов получить сообщение, сервер создаёт копию сообщения
и отправляет ему;
5. ***Consumer*** получает сообщение, отправляет подтверждение;
6. ***Broker***, получив подтверждение, удаляет копию сообщения и само сообщение из
памяти.

![1e8006fbb2212da5d284f246fa0f64bc.png](RabbitMQ,%20AMQP,%20RPC%206cfb8a8d805f43ebaafa9e9cbf3eead6/1e8006fbb2212da5d284f246fa0f64bc.png)

### RPC (Remote procedure call)

Для обмена информацией между клиентом и сервером используют каналы, которые создаются в рамках определенного подключения.

![e8lrjjvfhlx_bnd4lusfntmukmo.jpeg](RabbitMQ,%20AMQP,%20RPC%206cfb8a8d805f43ebaafa9e9cbf3eead6/e8lrjjvfhlx_bnd4lusfntmukmo.jpeg)

Каждый канал изолирован от другого, в синхронном случае невозможно выполнить команду, не получив ответ.

Для отправки параллельно можно открыть несколько каналов, что неплохо занимает память, так как каждый канал - отдельный **Erlang** процесс. Открытие канала на каждую операцию тоже приведет к большим затратам.

Поэтому чем больше каналов имеется в рамках соединения, тем **больше памяти использует RabbitMQ** для управления таким соединением.

<aside>
⚠️ Открывать новое соединение для каждой операции, настоятельно не рекомендуется, поскольку это **приведет к большим затратам**.

</aside>

Каналы также должны быть постоянными, но многие ошибки протокола приводят к закрытию канала, поэтому срок службы канала может быть короче, чем у соединения.

![iqneq-_gmg-yq8bkk2-3_-mkvky.jpeg](RabbitMQ,%20AMQP,%20RPC%206cfb8a8d805f43ebaafa9e9cbf3eead6/iqneq-_gmg-yq8bkk2-3_-mkvky.jpeg)

### Где используется RabbitMQ?

В контексте микросервисов протокол **AMQP** и его реализацию в **RabbitMQ** часто используют для **асинхронного взаимодействия** между сервисами.

В контексте **IIOT** протокол **AMQP** и его реализацию в RabbitMQ используют для обмена данными между серверами (сервер-сервер). Также используют плагин [MQTT Plugin RabbitMQ](https://www.rabbitmq.com/mqtt.html) являющегося реализацией протокола [MQTT](https://ru.wikipedia.org/wiki/MQTT)  для передачи данных между датчиком и сервером в низкоскоростных средах с высокой задержкой.

### Exchange

***Exchange*** - обменник или точка обмена, в который отправляют сообщения. Распределяет сообщения в одну или несколько очередей на основе созданных ***billing***.

<aside>
💡 Не является ***Erlang*** процессом. 1 тысяча ***Exchange*** может потреблять всего 1Мб памяти.

</aside>