# Parsing logs, labels и retention

## Цель

Сделать logs searchable и экономичными, не потеряв контекст и не создав cardinality explosion.

## Prerequisites

Structured logs, Loki/log aggregation, labels, timestamps и incident investigation.

## In scope

Парсинг извлекает severity, service, environment, request ID и event type; labels должны иметь bounded values. Тело лога хранит variable details. Retention выбирается по incident, compliance, cost и recovery requirements.

Доставка может дублировать или терять события; расследование должно знать delivery guarantees и delay.

## Практика

Соберите structured logs, добавьте parser/labels, создайте high-cardinality label и измерьте стоимость, затем исправьте schema.

## Следующие темы

`04-Distributed-Tracing-and-OpenTelemetry-Concepts.md`, correlation и retention policy.
