# GitOps и promotion manifests

## Цель

Понять reconciliation deployment из Git и границы между source of truth, cluster state и runtime.

## Prerequisites

Git, Kubernetes objects, immutable images, controllers и CI promotion.

## In scope

Git содержит desired manifests, controller сравнивает их с cluster state и применяет diff. Review/merge становится change control, а drift обнаруживается reconciliation. Secrets должны доставляться безопасным механизмом, не plaintext в Git.

Promotion меняет ссылку на digest или environment overlay, но не пересобирает image.

## Практика

Создайте dev/prod overlays, продвиньте digest merge request, внесите ручной drift в cluster и подтвердите его возврат controller.

## Следующие темы

`04-Pipeline-Testing-Security-and-Troubleshooting.md`, Helm/Kustomize и Production change safety.
