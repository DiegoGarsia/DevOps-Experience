# Архитектура GitLab CI/CD

## Цель

Связать общую модель CI/CD с GitLab components и понимать, где искать проблему pipeline.

## Prerequisites

Все предыдущие темы CI/CD, Git, Docker images и runners.

## In scope

GitLab repository и merge request запускают pipeline. GitLab coordinator создаёт jobs, runner исполняет их, registry хранит images, artifact storage сохраняет результаты, а environments фиксируют deployments. Protected branches/environments и approvals образуют trust boundary.

Типовой поток:

```text
Merge request
  ↓ pipeline
lint/test/security
  ↓
build image + push digest
  ↓
staging environment
  ↓ approval/verification
production deployment
```

Конфигурация pipeline должна быть reviewable, modular и иметь одинаковые правила для всех проектов. Shared templates полезны только при versioning, compatibility policy и понятном owner.

## Диагностика

Проверяйте pipeline source, rule evaluation, job status, runner assignment, artifact availability, registry access, environment protection и deploy logs.

## Типичные ошибки

- скрытая логика в shared template;
- runner имеет лишние credentials;
- production environment не защищён;
- artifact хранится меньше rollback window;
- pipeline green, но post-deploy verification отсутствует.

## Практика

Соберите GitLab pipeline с reusable job, protected staging/production environments, image registry и rollback job, затем разберите отказ runner и отказ registry.

## Следующие темы

Terraform, Ansible, Kubernetes deployment и CI/CD troubleshooting.
