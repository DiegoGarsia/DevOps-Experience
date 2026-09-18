# API objects и desired state

## Цель

Понять структуру Kubernetes object и различие между желаемым состоянием, текущим состоянием и наблюдаемым status.

## Prerequisites

API Server, YAML/JSON, declarative model и reconciliation.

## In scope

Object обычно содержит `apiVersion`, `kind`, `metadata`, `spec` и `status`. `spec` задаёт desired state, `status` публикует observed state controller. `metadata.name`, namespace, labels, annotations, owner references и resourceVersion влияют на identity и lifecycle.

Declarative apply должен быть повторяемым и versioned в Git. Не редактируйте status как замену исправлению spec. Owner references и garbage collection связывают дочерние objects с владельцем.

## Диагностика

Сравнивайте `spec`, `status`, events, managed fields и controller logs. При конфликте update проверяйте resourceVersion и конкурирующих writers.

## Типичные ошибки

- путать labels с annotations;
- хранить desired state только в cluster;
- редактировать generated child object вместо parent;
- менять status вручную;
- не задавать namespace явно.

## Практика

Создайте object, измените его declarative manifest, найдите spec/status и owner references, затем удалите parent и наблюдайте garbage collection.

## Следующие темы

`04-etcd-Consistency-and-Basic-Recovery.md`, scheduler и controllers.
