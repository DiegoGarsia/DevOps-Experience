# Rollout и rollback Kubernetes

## Цель

Научиться безопасно менять Pod template и возвращать прежнюю версию по наблюдаемым сигналам.

## Prerequisites

Deployments/ReplicaSets, probes, Services, immutable images и observability basics.

## In scope

Deployment strategy задаёт rolling parameters: maxSurge, maxUnavailable, progress deadline и revision history. Новый rollout должен проходить readiness, application health и capacity constraints. `kubectl rollout status/history/undo` управляют lifecycle, но не заменяют проверку пользовательского сигнала.

Rollback возвращает предыдущий Pod template, но не отменяет database migrations, external side effects или уже обработанные messages. Поэтому schema changes должны быть backward-compatible, а rollback plan — частью release design.

## Диагностика

Сопоставляйте revision, ReplicaSets, Pod readiness, events, image digest, Service endpoints и metrics. Остановите rollout до rollback, если проблема не подтверждена.

## Типичные ошибки

- rollback по одному liveness alert;
- mutable tag делает rollback непредсказуемым;
- нет capacity для surge;
- rollback приложения несовместим с schema;
- history ограничена слишком коротко.

## Практика

Выполните rollout хорошей версии, дефектной версии с failing readiness, остановите rollout, соберите evidence и выполните rollback с verification.

## Следующие темы

`22-Cluster-Installation-and-Lifecycle.md`, CI/CD promotion и production release safety.
