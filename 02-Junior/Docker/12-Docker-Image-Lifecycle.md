# Lifecycle Docker image

## Цель

Понять полный lifecycle образа от source change до удаления и определить, где контролируются безопасность, provenance и стоимость хранения.

## Prerequisites

Images/layers, Dockerfile, cache, registry и CI/CD basics.

## In scope

```text
source
  ↓ build
image
  ↓ scan/sign
registry
  ↓ pull by digest
runtime
  ↓ observe and promote
production
  ↓ retention/garbage collection
cleanup
```

Build должен иметь version и metadata, scan — policy, registry — access control и retention, runtime — проверку digest и compatibility. Удаление старых образов требует знания references, rollback window и текущих deployments.

Promotion означает перемещение уже созданного digest между environments. Повторная сборка из того же source может дать другой результат из-за base image, dependency или build time.

## Диагностика

Для проблем доставки проверяйте commit, build log, image digest, scan result, registry manifest, pull event и runtime image ID.

## Типичные ошибки

- удалять образы до завершения rollback window;
- считать tag audit trail;
- не хранить build metadata;
- не проверять multi-architecture manifest;
- публиковать unsigned artifact.

## Практика

Постройте lifecycle для test image: соберите, подпишите или промаркируйте digest, продвиньте его в staging, выполните rollback и удалите только безопасные версии.

## Следующие темы

CI/CD artifacts, immutable promotion и Kubernetes deployment.
