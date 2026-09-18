# Логи, Loki и blackbox monitoring

## Цель

Понимать различие между внутренними логами и проверкой сервиса снаружи и безопасно искать события по контексту.

## Prerequisites

journald, structured logs, HTTP/DNS/TLS и metrics.

## In scope

Log aggregation принимает события от host/application, нормализует timestamp и metadata, индексирует ограниченный набор labels и хранит body. В Loki labels должны иметь bounded cardinality; request ID, user и stack trace лучше оставлять в содержимом.

Blackbox exporter проверяет endpoint с точки зрения клиента: DNS, TCP, TLS, HTTP status, latency и body. Это дополняет whitebox metrics приложения, но не объясняет внутреннюю причину.

## Диагностика

Сначала проверяйте, поступают ли logs и blackbox probes, затем фильтруйте по service/environment/request ID и сопоставляйте с metrics. Учитывайте clock skew, delivery delay и sampling.

## Типичные ошибки

- label на raw URL/request ID;
- хранить secret в logs;
- blackbox probe из неправильной сети;
- считать отсутствие log отсутствием события;
- retention без оценки стоимости и требований аудита.

## Практика

Соберите structured logs и blackbox checks для HTTPS endpoint, сломайте DNS/TLS/HTTP отдельно и сопоставьте сигналы.

## Следующие темы

`08-SLI-Event-Detection-and-Basic-Investigation.md`, alerting и tracing.
