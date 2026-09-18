# Immutable artifacts и promotion

## Цель

Понять, почему один проверенный artifact должен продвигаться между средами без пересборки.

## Prerequisites

Images/digests, artifacts, environments, registry и Git refs.

## In scope

Immutable artifact имеет content identity: image digest, package checksum или подписанный bundle. Pipeline строит его один раз, публикует metadata и затем использует ту же ссылку в staging, pre-production и production.

```text
build once
  ↓ scan/test/sign
promote same digest
  ↓ staging verification
promote same digest
  ↓ production verification
```

Configuration environment не должна менять бинарный artifact. Если требуется rebuild для каждой среды, невозможно доказать, что проверенный staging artifact равен production artifact.

## Диагностика

Сравнивайте commit, image digest, package lock, SBOM и deployment manifest. При расхождении ищите повторный build, mutable tag, cache или разный base image.

## Типичные ошибки

- пересобирать image перед production;
- использовать tag вместо digest;
- изменять image вручную в registry;
- не хранить provenance;
- смешивать configuration и artifact content.

## Практика

Соберите image, продвиньте его в staging и production по digest, затем измените tag и докажите, что deployment использует прежний immutable artifact.

## Следующие темы

`09-Rollback-and-Deployment-Strategies.md`, registry lifecycle и Kubernetes rollout.
