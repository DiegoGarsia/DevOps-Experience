# Correlation metrics, logs и traces

## Цель

Собирать единый incident narrative из сигналов разной природы.

## Prerequisites

PromQL, structured logs, tracing, request ID и SLI.

## In scope

Metrics показывают масштаб/время, logs — детали события, traces — путь запроса. Correlation требует общего service, environment, instance, request/trace ID и согласованных timestamps.

Начинайте с SLI anomaly, переходите к aggregate metrics, затем к конкретному trace и связанным log events. Не заменяйте trace ID на high-cardinality metric label.

## Практика

Создайте latency incident, найдите affected route по metrics, конкретный trace и application log, затем подтвердите database cause.

## Следующие темы

`06-Prometheus-HA-Federation-and-Remote-Write.md`, alert design и incident response.
