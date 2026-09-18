# Load balancing и health checks

## Цель

Понять, как распределять запросы между backend и почему доступность порта не равна готовности приложения.

## Prerequisites

Proxy, HTTP, TCP и API health contract.

## In scope

Балансировщик выбирает backend по алгоритму round-robin, least connections, weighted или hash-based. Выбор должен учитывать состояние, session affinity и связь между нагрузкой и capacity. Stateless backend проще масштабировать; stateful session требует внешнего хранения или контролируемой affinity.

Health check отвечает на вопрос, можно ли направлять трафик. TCP check подтверждает только listener. HTTP liveness может показать, что процесс отвечает, но readiness должен проверять реальные зависимости с разумным timeout. Слишком глубокий health check может сам перегрузить систему или вывести backend из rotation при временной ошибке зависимости.

```text
request
  ↓ listener
health state
  ├── healthy → candidate pool
  └── unhealthy → drain or remove
```

Drain нужен для graceful deployment: новые запросы не направляются backend, а активные получают время завершиться.

## Диагностика

Проверяйте размер backend pool, распределение запросов, health transitions, connection reuse, retries и latency по каждому upstream. Сопоставляйте балансировщик с приложением, а не только с network port.

## Типичные ошибки

- один health endpoint для liveness и readiness;
- проверка зависимости на каждом probe без timeout;
- отсутствие drain при остановке;
- retry на балансировщике вместе с retry клиента;
- uneven distribution из-за long-lived connections.

## Практика

Настройте два backend с различными задержками, сравните алгоритмы, выключите зависимость одного backend и проверьте, исключается ли он без удаления healthy экземпляра.

## Следующие темы

`17-Firewalls-and-Packet-Filtering.md`, `18-NAT-and-Conntrack.md` и Junior load-balancer operations.
