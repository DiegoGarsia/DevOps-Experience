# etcd: consistency и базовое recovery

## Цель

Понять роль etcd как согласованного хранилища control-plane state и почему его backup критичен для cluster recovery.

## Prerequisites

Distributed storage basics, API Server, desired state и backup/restore.

## In scope

etcd хранит Kubernetes objects и использует consensus между members. API Server обращается к etcd через защищённый transport. Потеря quorum или высокая latency etcd делает control plane неспособным надёжно читать/записывать состояние, хотя уже запущенные Pods могут продолжать работу.

Backup должен быть snapshot-consistent, зашифрованным, доступным вне failure domain и регулярно проверяемым restore. Восстановление etcd — cluster-level operation: нельзя просто заменить один файл data directory на работающем member.

## Диагностика

Проверяйте member health, quorum, disk latency, database size, defrag policy, certificate expiry и API Server errors. Не запускайте destructive recovery без остановки writers и documented runbook.

## Типичные ошибки

- хранить snapshot на том же диске;
- считать несколько members заменой backup;
- делать defrag во время перегрузки без оценки;
- восстанавливать etcd в другой cluster без понимания identity;
- не тестировать snapshot restore.

## Практика

Сделайте test etcd snapshot, проверьте metadata и выполните восстановление отдельного lab cluster, затем проверьте доступность API objects.

## Следующие темы

`05-Scheduler.md`, `06-Controllers-and-Reconciliation.md` и StrongMiddle etcd recovery.
