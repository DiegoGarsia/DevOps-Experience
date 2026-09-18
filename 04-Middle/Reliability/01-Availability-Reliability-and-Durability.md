# Availability, reliability и durability

## Цель

Различать свойства системы и выбирать измеримые controls вместо общего требования «надёжно».

## Prerequisites

Linux/networking, backup, replication, observability и failure scenarios.

## In scope

Availability — доля времени/операций, когда сервис доступен. Reliability — вероятность корректно работать в заданных условиях и периоде. Durability — вероятность сохранения данных после отказа. Redundancy может повысить availability, но усложняет consistency и operations.

## Практика

Для сервиса с базой определите availability, durability и reliability goals, failure modes и измеримые signals.

## Следующие темы

`02-SPOF-Redundancy-and-Failure-Domains.md`, SLO и RPO/RTO.
