# Testing, security и troubleshooting pipeline

## Цель

Проверять pipeline как систему: code, runner, dependencies, credentials, artifact и deployment.

## Prerequisites

CI jobs/runners, secrets, artifacts, images, registry и Kubernetes deployment.

## In scope

Checks должны покрывать lint, tests, reproducibility, dependency/image scanning, secret exposure, artifact digest и post-deploy verification. При failure сначала определяйте stage и runner context, затем проверяйте input/output boundary.

## Практика

Сломайте runner, dependency download, secret permission, registry push и health verification по отдельности. Для каждой ошибки сохраните log/evidence, исправьте и повторите pipeline.

## Следующие темы

Terraform/Ansible integration и Production CI/CD troubleshooting.
