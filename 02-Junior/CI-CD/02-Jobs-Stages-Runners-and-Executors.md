# Jobs, stages, runners и executors

## Цель

Различать описание pipeline, unit выполнения и среду, в которой реально запускается команда.

## Prerequisites

Pipeline lifecycle, Linux process model, containers и Git.

## In scope

Job — отдельная задача с image/environment/script и результатом. Stage группирует jobs логически, но DAG-зависимости могут позволять параллельный запуск. Runner получает job, подготавливает workspace, запускает executor и отправляет status/log/artifacts.

Executor может использовать shell, Docker, VM или Kubernetes. Изоляция, cache, network access, filesystem и credentials зависят от executor, поэтому одинаковый YAML не гарантирует одинаковое поведение.

```text
Pipeline scheduler
  ↓ job assignment
Runner
  ↓ executor
Workspace + environment
  ↓
script
```

## Диагностика

Проверяйте runner tags/capacity, image, workspace, network, permissions, service account и version. Отличайте scheduler pending от ошибки внутри job.

## Типичные ошибки

- privileged runner для всех pipelines;
- shared runner с остатками чужих secrets;
- ожидание Docker-in-Docker без понимания daemon;
- отсутствие timeout и concurrency limit;
- скрытая зависимость от локального runner state.

## Практика

Запустите одну job на shell и container executor, сравните environment, filesystem, network и cache, затем ограничьте job подходящим tag.

## Следующие темы

`03-Artifacts-Cache-and-Dependencies.md`, variables и rules.
