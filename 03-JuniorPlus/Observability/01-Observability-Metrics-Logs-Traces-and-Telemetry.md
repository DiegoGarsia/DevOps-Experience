# Observability: metrics, logs, traces и telemetry

## Цель

Различать сигналы наблюдаемости и выбирать сигнал, который отвечает на конкретный operational вопрос.

## Prerequisites

Linux processes, networking, HTTP, logs и базовая статистика.

## In scope

Metrics — числовые временные ряды для rates, saturation и trends. Logs — события с контекстом. Traces — путь одного запроса через распределённые компоненты. Telemetry — общий поток этих signals от producer к storage/query/alerting.

Наблюдаемость отвечает не только «жив ли процесс», но и «почему пользовательская операция медленная или неуспешная». Нужны correlation ID, timestamp с единым временем и связь между signal types.

## Диагностика

Начинайте с user symptom и SLI, затем переходите к metrics, logs, traces и ресурсам. Сигнал без labels/metadata, retention и owner быстро теряет operational value.

## Типичные ошибки

- собирать всё без cardinality/cost policy;
- health check вместо user SLI;
- логировать stack trace без request ID;
- метрики без единиц и семантики;
- считать отсутствие telemetry доказательством отсутствия ошибки.

## Практика

Добавьте к test service request metric, structured log и trace/correlation ID, затем расследуйте искусственно медленный запрос по трём сигналам.

## Следующие темы

`02-Metrics-Labels-Cardinality-and-Time-Series.md`, Prometheus и log aggregation.
