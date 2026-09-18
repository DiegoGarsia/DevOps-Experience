# Rollback и deployment strategies

## Цель

Понимать, как стратегия release ограничивает blast radius и как заранее подготовить обратимое изменение.

## Prerequisites

Immutable artifacts, environments, health checks и load balancing.

## In scope

Rolling deployment постепенно заменяет экземпляры. Blue-green держит две среды и переключает traffic. Canary направляет небольшой процент или выбранный сегмент пользователей на новую версию. Recreate проще, но создаёт downtime.

Rollback возможен только если:

- старый artifact доступен;
- schema/data compatibility сохранена;
- traffic можно вернуть;
- secrets и config совместимы;
- команда знает trigger и owner.

Database migration часто нельзя просто откатить. Используйте backward-compatible expand/contract pattern: добавить новое, перевести consumers, удалить старое после подтверждения.

## Типичные ошибки

- rollback приложения при несовместимой схеме базы;
- canary без signal и traffic control;
- blue-green без capacity для двух сред;
- rolling update без readiness;
- rollback по одному шумному alert.

## Практика

Проведите rolling и canary release test, добавьте дефект в новую версию, обнаружьте его по signal, остановите promotion и выполните rollback.

## Следующие темы

`10-GitLab-CI-CD-Architecture.md`, Kubernetes rollout и Production release safety.
