# Pipeline сборки container image

## Цель

Построить воспроизводимый путь от commit до подписанного immutable image.

## Prerequisites

Git, Dockerfile, build cache, registry, SBOM и CI jobs.

## In scope

Pipeline должен выполнить lint/test, собрать image, проверить digest, просканировать dependencies, сформировать metadata/SBOM и отправить artifact в registry. Build context и credentials должны быть ограничены.

Повторная сборка одного commit должна давать проверяемый результат; environment promotion использует тот же digest.

## Типичные ошибки

- build на runner отличается от локального;
- secret попал в context/layer;
- tag используется вместо digest;
- scan выполняется после deployment;
- cache скрывает dependency change.

## Практика

Создайте pipeline build/test/scan/push, добавьте digest output и повторите его на чистом runner.

## Следующие темы

`02-Registry-to-Cluster-Deployment.md`, immutable promotion и Kubernetes.
