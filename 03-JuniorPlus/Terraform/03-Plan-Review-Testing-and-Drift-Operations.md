# Plan review, testing и drift operations

## Цель

Превратить Terraform plan в проверяемое изменение с review, policy, drift detection и recovery.

## Prerequisites

Modules, state migration, remote locking, testing и CI/CD.

## In scope

CI должен запускать format/validate, static policy, plan с remote lock и сохранять безопасный plan artifact. Review проверяет replacement, network exposure, capacity, data loss и provider changes.

Drift классифицируется как ожидаемый внешний change, emergency fix или ошибка ownership. Исправление может быть import/state update, configuration change или rollback.

## Практика

Создайте drift вручную, обнаружьте его scheduled plan, классифицируйте и восстановите desired state с approval.

## Следующие темы

Ansible scale, CI integration и Production IaC troubleshooting.
