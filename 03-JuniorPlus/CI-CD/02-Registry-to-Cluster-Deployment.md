# От registry до cluster deployment

## Цель

Понять доверенный путь image и manifest от CI runner до kubelet/runtime.

## Prerequisites

Image pipeline, registry auth, Kubernetes Pods, CRI и Secrets.

## In scope

CI публикует digest, deployment manifest ссылается на него, cluster получает pull credentials, node скачивает image через CRI и запускает Pod. Каждый этап должен иметь audit event и понятную ошибку.

Диагностируйте отдельно registry TLS/auth, image pull, admission, scheduling, kubelet, runtime и application startup.

## Практика

Продвиньте image в test cluster, отзовите pull credential, восстановите его и проверьте, что deployment использует ожидаемый digest.

## Следующие темы

`03-GitOps-and-Manifest-Promotion.md`, rollout и rollback.
