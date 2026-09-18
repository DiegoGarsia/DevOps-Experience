# API Server Kubernetes

## Цель

Понять API Server как authentication, validation, admission и persistence boundary всего кластера.

## Prerequisites

HTTP/API contracts, TLS/PKI, authentication/authorization и control-plane model.

## In scope

API Server принимает HTTP requests к resources, выполняет authentication, authorization, defaulting, validation, admission и записывает объект в etcd. Watch clients получают изменения для controllers, scheduler, kubelet и operators.

API Server stateless относительно desired objects: authoritative state хранится в etcd, а API Server предоставляет его через versioned API. Высокая доступность требует нескольких экземпляров API Server и надёжного доступа к etcd.

## Диагностика

Разделяйте TLS/authentication error, RBAC `403`, validation `422`, admission rejection, etcd latency и network timeout. `kubectl` не является источником причины сам по себе: смотрите response, events и API Server logs.

## Типичные ошибки

- дать service account cluster-admin;
- считать успешный HTTP `201` доказательством готовности workload;
- отключать TLS verification;
- менять object через неподходящую API version;
- игнорировать resourceVersion и conflict при update.

## Практика

Создайте object с намеренно неверной schema, затем проверьте auth, RBAC и admission отказ отдельно. Отследите watch-события после изменения object.

## Следующие темы

`03-API-Objects-and-Desired-State.md`, etcd и controllers.
