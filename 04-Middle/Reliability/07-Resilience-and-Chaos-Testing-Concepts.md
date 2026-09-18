# Resilience и chaos testing concepts

## Цель

Проверять failure assumptions контролируемым экспериментом с ограниченным blast radius.

## Prerequisites

Failure domains, graceful degradation, observability, rollback и recovery.

## In scope

Эксперимент формулирует hypothesis, steady state, fault, safety guard, abort condition и expected signal. Начинайте с staging/sandbox, одного failure, короткого окна и готового rollback.

## Практика

Отключите один backend, увеличьте latency dependency и заполните disk в lab. Для каждого сравните steady state/impact/recovery и оформите learning.

## Следующие темы

Production game days, distributed failure scenarios и architecture.
