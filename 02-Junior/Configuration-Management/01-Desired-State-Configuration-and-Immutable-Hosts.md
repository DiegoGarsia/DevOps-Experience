# Desired state configuration и immutable hosts

## Цель

Различать mutable configuration management и immutable infrastructure и выбирать границу управления host.

## Prerequisites

Linux, package management, systemd, images, Ansible и Terraform basics.

## In scope

Desired state описывает, каким должен быть ресурс: пакет установлен, сервис запущен, файл имеет нужное содержимое и права. Configuration management приводит текущий host к этому состоянию и повторно проверяет convergence.

Mutable host обновляется на месте. Такой подход удобен для небольших систем, но со временем накапливает drift и ручные исключения. Immutable host создаётся из versioned image, заменяется целиком и минимизирует ручную историю. Runtime state должен находиться во внешнем storage, а bootstrap — быть воспроизводимым.

```text
image/configuration
  ↓ provision
host
  ↓ observe
drift
  ├── converge in place
  └── rebuild/replace immutable host
```

Ни один подход не отменяет backup, patch management, secret rotation и проверку результата. Immutable не означает «никогда не менять»: меняется source image и выполняется controlled replacement.

## Типичные ошибки

- смешивать ручные SSH hotfix и declarative automation;
- хранить state на ephemeral host;
- считать Ansible role заменой backup;
- immutable replacement без capacity и rollback;
- не записывать manual exception.

## Практика

Настройте host baseline через Ansible, внесите ручной drift, восстановите desired state, затем соберите аналогичный host из image и сравните operational risk.

## Следующие темы

Terraform modules, CI/CD immutable artifacts, containers и Kubernetes.
