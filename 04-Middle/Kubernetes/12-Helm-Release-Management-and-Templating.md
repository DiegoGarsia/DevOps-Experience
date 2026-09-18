# Helm: release management и templating

## Цель

Использовать Helm как versioned packaging layer, не скрывая rendered Kubernetes resources и operational risk.

## Prerequisites

Kubernetes objects, YAML, ConfigMaps/Secrets, rollout и GitOps.

## In scope

Chart объединяет templates, values, metadata и dependencies. Release хранит rendered revision и позволяет upgrade/rollback, но не гарантирует application compatibility или data rollback.

Values должны иметь schema/defaults и environment ownership. Rendered manifests нужно проверять lint, diff, policy и schema до apply. Secrets не следует хранить в values plain text.

## Диагностика

Сравнивайте chart version, values, rendered manifest, release history, hooks и actual objects. Ошибка template отличается от admission, scheduler и runtime failure.

## Практика

Создайте chart для приложения, добавьте schema/values для двух environments, выполните diff, upgrade с дефектом и rollback.

## Следующие темы

`13-Kustomize-Overlays-and-Configuration.md`, release safety и GitOps.
