# Безопасность Docker

## Цель

Сформировать минимальную security baseline для image, daemon, container, network и secret delivery.

## Prerequisites

Capabilities, seccomp, images, registry, permissions и Docker architecture.

## In scope

Основные controls:

- минимальный base image и обновляемые dependencies;
- image scanning, SBOM и provenance;
- non-root user;
- drop capabilities и read-only root filesystem, где возможно;
- seccomp/LSM policy;
- resource limits;
- отсутствие secret в image layers и logs;
- ограничение Docker socket и daemon API;
- сегментация network и минимальные published ports.

Docker socket фактически предоставляет управление host daemon, поэтому его mount в контейнер должен рассматриваться как privileged access. Rootless mode снижает риск, но меняет возможности networking/storage.

## Диагностика

Проверяйте image history, user, capabilities, mounts, devices, network exposure, socket access и package vulnerabilities. Security check должен учитывать runtime configuration, а не только image scan.

## Типичные ошибки

- `--privileged` для устранения любой ошибки;
- root process и writable root filesystem без причины;
- secret через `ARG` или layer;
- exposed Docker socket;
- scanner без remediation policy.

## Практика

Соберите baseline image с non-root user, минимальными capabilities и read-only root filesystem, затем проверьте, какая операция требует отдельного writable mount.

## Следующие темы

`11-Docker-Troubleshooting.md`, supply chain security и Kubernetes security contexts.
