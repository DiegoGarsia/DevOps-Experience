# Security contexts и Pod isolation

## Цель

Настроить Pod с минимальными privileges и понять, как security context взаимодействует с runtime, filesystem и policy.

## Prerequisites

Linux permissions, capabilities, seccomp, containers, RBAC и admission.

## In scope

SecurityContext задаёт runAsUser/group, fsGroup, privileged, allowPrivilegeEscalation, capabilities, readOnlyRootFilesystem и seccomp profile. Pod и container settings имеют разную область действия.

Изменение UID может сломать volume permissions; read-only root требует writable tmpfs/volume; dropping capability может раскрыть ошибку application. Security baseline должна быть совместима с workload и enforced policy.

## Практика

Переведите контейнер на non-root/read-only/drop capabilities, найдите единственный необходимый writable path и проверяйте через admission/policy.

## Следующие темы

`17-Kubernetes-Troubleshooting-Model.md`, RBAC design и supply chain.
