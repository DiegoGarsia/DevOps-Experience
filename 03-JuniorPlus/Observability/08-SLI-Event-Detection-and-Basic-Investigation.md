# SLI, event detection и базовая investigation

## Цель

Связать telemetry с пользовательским результатом и начать расследование от symptom, а не от любимого инструмента.

## Prerequisites

Metrics, logs, traces, PromQL, dashboards и alerting.

## In scope

SLI измеряет свойство сервиса: availability, latency, correctness, freshness или throughput. Хороший SLI связан с user journey и имеет чёткий numerator/denominator. Host CPU может быть полезной diagnostic metric, но не обязательно SLI.

Базовая investigation:

1. подтвердить symptom и scope;
2. определить начало и affected population;
3. сравнить SLI с baseline;
4. сопоставить metrics, logs, traces и changes;
5. сформулировать гипотезу;
6. проверить её минимальным безопасным запросом.

## Типичные ошибки

- SLI равен availability процесса;
- alert без user impact;
- начинать с одного log line;
- игнорировать deployment/change timeline;
- изменять систему до сохранения evidence.

## Практика

Создайте incident с повышенной latency, но нормальным host CPU. Через SLI, logs, traces и dependency metrics найдите источник и подтвердите восстановление.

## Следующие темы

Observability Middle, SLO/error budget и Production incident investigation.
