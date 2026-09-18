# Remote state, locking и concurrency

## Цель

Понять, почему shared state должен иметь единственный источник записи и lock от одновременных apply.

## Prerequisites

Terraform state/plan/apply и storage access control.

## In scope

Remote backend хранит state вне локального рабочего каталога и может предоставлять encryption, versioning, locking и audit. Lock не даёт двум операциям одновременно менять один state и конфликтовать с реальной инфраструктурой.

CI job должен получать backend credentials с минимальными правами, использовать lock timeout и удалять lock после корректного завершения. Force-unlock допустим только после проверки, что другой apply действительно завершён.

## Диагностика

Различайте network/backend outage, lock held by active run, stale lock и permission error. Проверяйте CI jobs, backend logs, state version и process, удерживающий lock.

## Типичные ошибки

- отключать locking для ускорения;
- вручную удалять lock без проверки active run;
- давать всем проектам доступ к одному state;
- хранить backend secret в репозитории;
- применять разные workspace к одному environment.

## Практика

Запустите два concurrent plan/apply, зафиксируйте поведение lock, завершите один процесс аварийно и выполните безопасный recovery stale lock.

## Следующие темы

`08-Import-Drift-and-State-Migration.md`, workspaces и testing.
