# Безопасность контейнеров и images

## Цель

Применить threat model к image build и runtime configuration.

## Prerequisites

OCI/images, Docker security, capabilities, seccomp, registry и dependency management.

## In scope

Image security включает минимальный base, pinned dependencies, scan, SBOM, signature/provenance и controlled registry. Runtime security включает non-root, drop capabilities, read-only filesystem, ограниченные devices/mounts, seccomp/LSM и resource limits.

Scan finding требует triage: exploitability, reachability, severity, remediation и exception owner. Обновление base image должно быть воспроизводимым и проверяться тестами.

## Диагностика

Проверяйте image layers/history, user, capabilities, mounts, digest, signatures, vulnerabilities и runtime events. Не считайте clean scan доказательством безопасной configuration.

## Типичные ошибки

- latest base image;
- root + privileged runtime;
- secret в layer;
- игнорирование transitive dependency;
- exception без срока и owner.

## Практика

Соберите image, создайте SBOM/scan report, исправьте критическую dependency и проверьте runtime baseline.

## Следующие темы

`06-Dependency-Security-SBOM-and-Vulnerabilities.md`, supply chain и Kubernetes security.
