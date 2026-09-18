# State, consistency и scaling

## State

State — данные, без которых система не может продолжить работу корректно: database rows, queue offsets, Terraform state, Kubernetes cluster state, certificates и sessions. Stateless process можно заменить, stateful data требует ownership, durability и recovery plan.

## Consistency

Consistency — не одно универсальное «данные всегда одинаковы». Нужно определить:

- кто source of truth;
- допустимую stale window;
- порядок операций;
- read-after-write expectation;
- failure при network partition;
- способ reconciliation.

Replication lag означает, что read replica может отставать. Cache может вернуть stale value. Async queue меняет момент, когда write считается обработанным.

## Scaling

- vertical: больше CPU/RAM/IOPS;
- horizontal: больше instances;
- partitioning/sharding: распределение data/work;
- caching: уменьшение нагрузки ценой invalidation;
- queue: decoupling и backpressure;
- connection pooling: ограничение дорогих connections.

Scaling bottleneck может быть не application: database locks, network, registry, scheduler, storage или cardinality.

## Failure scenarios

- два writer без coordination → lost update/conflict;
- retry без idempotency → duplicate side effect;
- cache без invalidation → stale/incorrect result;
- retry storm → нагрузка усиливает failure;
- horizontal scale stateful app без shared storage → divergence.

## Middle-вопросы

- **Почему добавление replicas не помогло?** Ограничение может быть downstream или serial section.
- **Что такое backpressure?** Механизм замедления producer, когда consumer/storage не успевает.
- **Как выбирать consistency?** От business invariant и user impact, а не от модного инструмента.

## 30–60 секунд

«Перед scaling я определяю state, source of truth и bottleneck. Stateless replicas легко заменить, stateful components требуют consistency, durability и recovery. Horizontal scale без backpressure, pooling и idempotent retries может увеличить outage вместо производительности».

[[03-JuniorPlus/Databases/PostgreSQL-Internals]], [[04-Middle/Reliability/Availability-Backup-Recovery]], [[04-Middle/Messaging/Kafka-Fundamentals-and-Failures]].

## Термины и причинная цепочка

Strong consistency означает, что после успешного write последующий read видит его согласно выбранной модели. Eventual consistency допускает временное расхождение replicas, но система со временем сходится. Read-after-write — более узкое требование: конкретный client должен увидеть собственную запись.

Retry без idempotency может повторить side effect. Backpressure замедляет producer, когда consumer не успевает. Connection pooling ограничивает дорогие database connections, но pool слишком большого размера превращается в connection exhaustion.

## Performance chain

```text
request rate
→ queue/backpressure
→ worker concurrency
→ downstream calls
→ connection pool
→ database locks/I/O
→ response latency
```

Добавление replicas меняет capacity только для parallelizable work. Serial section, lock или один storage endpoint остаются bottleneck.

## Практическое задание

1. Напиши endpoint, который безопасно повторяется по idempotency key.
2. Добавь искусственный slow downstream и наблюдай queue/timeout/retry storm.
3. Ограничь connection pool и сравни latency/connection exhaustion.
4. Сымитируй stale cache и выбери invalidation/TTL policy.
5. Нарисуй source of truth и consistency contract для сервиса с database и queue.
