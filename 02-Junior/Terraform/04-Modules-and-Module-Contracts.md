# Modules и module contracts

## Цель

Научиться проектировать module как ограниченный интерфейс, а не как папку с случайно переиспользованным кодом.

## Prerequisites

Variables, resources, outputs и dependency graph.

## In scope

Module принимает inputs, создаёт resources и возвращает outputs. Контракт должен описывать типы, defaults, validation, side effects, ownership, lifecycle и version compatibility.

Хороший module скрывает implementation details, но не скрывает важные operational decisions: network, storage class, backup, security и naming. Изменение default может создать destructive plan, поэтому module interface versionируется.

```text
root module
  ↓ inputs
reusable module
  ↓ resources
outputs
  ↓
other modules or deployment
```

Не следует создавать module на каждый resource: граница должна соответствовать reusable capability и lifecycle.

## Типичные ошибки

- module с десятками несвязанных flags;
- output всего state;
- скрытый provider configuration;
- изменение naming без migration;
- отсутствие examples и tests.

## Практика

Создайте module для VM + network attachment, оформите README-контракт, validation, outputs и example environment.

## Следующие темы

`05-Dependency-Graph-and-Lifecycle.md`, state и remote locking.
