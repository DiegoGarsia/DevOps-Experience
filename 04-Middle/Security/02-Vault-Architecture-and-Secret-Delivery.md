# Vault architecture и secret delivery

## Цель

Понять secret manager как систему identity, policy, storage, lease и rotation, а не как зашифрованный файл.

## Prerequisites

Authentication/authorization, secret lifecycle, TLS/PKI и Kubernetes/CI trust boundaries.

## In scope

Secret manager получает identity workload, проверяет policy и выдаёт secret с TTL/lease. Delivery может быть API, agent, sidecar или sync controller. Audit должен показывать субъект и действие, но не значение secret.

HA требует storage consistency, unseal/key custody, renewal и recovery plan. Потеря manager не должна автоматически делать все workloads unusable без cache/rotation design.

## Практика

Настройте policy для двух roles, выдайте short-lived credential, отзовите lease и проверьте отсутствие доступа после expiration.

## Следующие темы

`03-Linux-Hardening-and-Secure-Baselines.md`, Kubernetes RBAC и Vault optional deep dive.
