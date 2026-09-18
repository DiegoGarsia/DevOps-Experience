# Variables и конфигурация pipeline

## Цель

Понять precedence и lifecycle pipeline variables и отделить конфигурацию от кода без потери воспроизводимости.

## Prerequisites

Environment variables, structured configuration, jobs и runners.

## In scope

Variable может быть задана в project/group, pipeline, job или environment scope. При конфликте важна documented precedence. Значение должно иметь тип, default, validation и ограниченный scope.

Разделяйте:

- build configuration;
- deployment target;
- feature/config flags;
- secret values;
- metadata commit и artifact.

Секретные variables должны быть masked/protected, не попадать в command echo, artifact, cache и diagnostic output. Конфигурация pipeline должна проходить lint и code review.

## Типичные ошибки

- одна переменная меняет смысл на разных стадиях;
- secret передан в Docker build argument и попал в layer;
- production variable доступна untrusted branch;
- отсутствие default приводит к пустому deployment;
- job скрывает эффективную конфигурацию.

## Диагностика

Проверяйте effective variables безопасным способом: имя, наличие и источник, но не печатайте secret value. Сравнивайте environment scope, branch protection и runner context.

## Практика

Опишите staging/production variables, добавьте masked secret, protected deployment и job, который безопасно проверяет наличие обязательных параметров.

## Следующие темы

`05-Secrets-and-CI-Trust-Boundaries.md`, rules/needs и environments.
