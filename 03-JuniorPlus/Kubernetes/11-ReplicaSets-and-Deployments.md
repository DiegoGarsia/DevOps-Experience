# ReplicaSets и Deployments

## Цель

Понять, как Deployment управляет версиями Pod через ReplicaSet и почему изменение template создаёт новый rollout.

## Prerequisites

Pods, labels/selectors, controllers, scheduler и container images.

## In scope

ReplicaSet поддерживает заданное число Pods по selector. Deployment управляет ReplicaSets, хранит rollout history и позволяет менять image, replicas и strategy.

Template hash отличает версии Pod. Изменение Pod template создаёт новый ReplicaSet; изменение только replicas не создаёт новую версию.

Контролируйте max unavailable/max surge, readiness и progress deadline. Rollout считается безопасным после готовности новых Pods и проверки application signals, а не после появления ReplicaSet.

## Диагностика

Сопоставляйте Deployment, ReplicaSets, Pod labels, events, conditions и image digest. Отдельно проверяйте `ProgressDeadlineExceeded`, unavailable replicas и старую версию.

## Типичные ошибки

- selector не соответствует template labels;
- mutable image tag;
- rollout без readiness;
- удаление старого ReplicaSet до проверки rollback;
- считать replicas availability application correctness.

## Практика

Разверните Deployment, обновите image с дефектом, остановите rollout по readiness, затем восстановите прежний digest и подтвердите состояние.

## Следующие темы

`12-Services-and-EndpointSlice.md`, probes и rollout/rollback.
