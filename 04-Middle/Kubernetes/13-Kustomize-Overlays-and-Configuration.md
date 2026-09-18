# Kustomize overlays и configuration

## Цель

Управлять различиями environments через overlays без копирования полных Kubernetes manifests.

## Prerequisites

Kubernetes objects, labels, ConfigMaps/Secrets, GitOps и declarative apply.

## In scope

Base содержит общие resources, overlay применяет patches, namespace, images, replicas и environment-specific configuration. Rendered output должен быть проверяемым lint/schema/policy инструментами.

Изменение image digest должно быть явным и reviewable. Secret values не следует хранить в открытом overlay; используйте external delivery или encrypted mechanism.

## Диагностика

Всегда исследуйте rendered manifest: порядок patches, selectors, namespace, labels и generated names. Ошибка overlay часто проявляется как valid YAML, но неправильная runtime semantics.

## Практика

Создайте base и два overlays, продвиньте digest только в staging, выполните diff и проверьте selectors/resources после render.

## Следующие темы

`14-Cluster-Upgrades-and-Node-Lifecycle.md`, Helm и GitOps.
