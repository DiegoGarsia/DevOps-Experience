# Troubleshooting container runtime

## Цель

Научиться отделять проблему image, runtime, namespace, cgroup, storage, network и самого приложения.

## Prerequisites

Все предыдущие темы Containers.

## In scope

Используйте последовательность:

```text
Symptom
  ↓
Container state and exit code
  ↓
Image/config
  ↓
Runtime events and logs
  ↓
Process and signal
  ↓
Mount/storage
  ↓
Network namespace/DNS/port
  ↓
Resources and OOM
  ↓
Application logs
```

`image pull` failure проверяется через registry/auth/digest; `create` failure — через mounts, capabilities и runtime config; `running but unavailable` — через listener, network namespace, published port и firewall; внезапное завершение — через exit code, signal и cgroup OOM.

Не удаляйте контейнер до сохранения inspect, logs, events и конфигурации. Restart может стереть важный контекст и замаскировать повторяемую ошибку.

## Практика

Подготовьте четыре failure injection: неверный image tag, неправильный mount permission, закрытый port и memory limit. Для каждого заполните таблицу symptom → facts → hypothesis → test → fix → verification.

## Следующие темы

Docker architecture, images, volumes, networks, registry и Kubernetes CRI.
