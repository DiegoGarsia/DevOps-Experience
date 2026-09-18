# Jobs и CronJobs

## Цель

Проектировать batch workload с понятным retry, completion, concurrency и retention behavior.

## Prerequisites

Pods, containers, backoff, storage, Secrets и Cron scheduling.

## In scope

Job отслеживает завершение одного или нескольких Pod и retry failure до backoffLimit. CronJob создаёт Jobs по расписанию и задаёт concurrencyPolicy, startingDeadline и history limits.

Batch operation должна быть идемпотентной: scheduler может повторить Pod после timeout, а предыдущая попытка могла частично изменить внешнюю систему. Нужно ограничивать overlap и хранить результат отдельно от ephemeral Pod logs.

## Диагностика

Проверяйте Job conditions, Pod exit code, backoff, schedule time, missed run, concurrency и external side effects.

## Типичные ошибки

- параллельные backup Jobs;
- отсутствие idempotency key;
- бесконечные failed Jobs;
- timezone mismatch;
- удаление history до расследования.

## Практика

Создайте CronJob с запретом overlap, симулируйте медленный запуск и ошибку, затем подтвердите cleanup policy и повторный safe run.

## Следующие темы

`05-Ingress-Gateways-and-Traffic-Entry.md`, scheduling и Production backup.
