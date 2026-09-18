# Kafka: middle-уровень

## Модель

```text
producer → topic/partition → broker/replicas → consumer group
```

Topic состоит из ordered partitions. Producer записывает record и получает offset. Consumer group распределяет partitions между members; в одной group partition обслуживается одним consumer в конкретный момент.

Broker хранит log segments. Leader partition обслуживает read/write, replicas копируют данные, ISR показывает синхронных участников. KRaft хранит metadata quorum без ZooKeeper.

## Delivery semantics

At-most-once теряет приоритет над duplicate, at-least-once допускает повтор при retry, exactly-once требует ограниченного transaction/idempotency contract и не делает внешний database side effect автоматически exactly-once.

Ordering гарантируется внутри partition, не между topic partitions. Retention удаляет старые records по policy, а не после чтения consumer.

## Что при отказе

- broker down → leader election, ISR shrink, возможная under-replication;
- partition leader down → producer/consumer ждут metadata refresh;
- consumer down → rebalance и lag;
- slow consumer → рост lag, storage pressure и отставание business result;
- controller/quorum failure → metadata operations недоступны;
- duplicate delivery → consumer handler должен быть idempotent.

## Наблюдение

Смотри consumer lag, under-replicated partitions, ISR, request latency, disk usage, controller quorum и failed produce/fetch requests. Lag сам по себе не говорит, что потеряны данные; он показывает скорость отставания.

## Вопросы

- **Почему partitions нужны для scale?** Они дают parallelism и placement, но ограничивают ordering.
- **Что такое ISR?** Реплики, достаточно синхронные для текущей политики.
- **Почему Kafka не обычная очередь?** Это durable partitioned log с replay/offset model.

## 30–60 секунд

«Kafka — распределённый log: topic разделён на ordered partitions, producer пишет offsets, consumer group делит partitions. Надёжность зависит от replication/ISR/acks, а обработка — от offset, lag и idempotency. При отказе broker смотрю leader/ISR/quorum, при отказе consumer — rebalance и lag».

[[04-Middle/Architecture/State-Consistency-and-Scaling]], [[04-Middle/Reliability/Availability-Backup-Recovery]], [[05-Production-Troubleshooting/Incident-Response]].

## Ack, offset и повтор

Producer может считать запись подтверждённой до полной replication в зависимости от `acks`. Consumer обычно сначала обрабатывает record, затем фиксирует offset. Если process падает между этими действиями, record будет прочитан снова; поэтому handler должен быть idempotent.

Consumer lag — разница между последним доступным offset и offset группы. Lag может расти из-за slow processing, rebalance, downstream latency, недостатка partitions или broker I/O.

## Практическое задание

1. Создай topic с несколькими partitions и наблюдай распределение group.
2. Останови consumer после обработки, но до commit offset.
3. Запусти его снова и докажи duplicate delivery.
4. Останови broker, проверь leader/ISR/under-replicated state.
5. Ограничь consumer processing и свяжи рост lag с latency downstream.
6. Объясни, какие гарантии сохраняются при каждом варианте failure.
