# ConfigMaps и application configuration

## Цель

Отделить конфигурацию приложения от image и понимать, когда изменение ConfigMap требует restart или reload.

## Prerequisites

API objects, Pods, environment variables, volumes и application configuration.

## In scope

ConfigMap хранит non-secret key/value или text data. Его можно передать как environment, отдельный file или argument. Environment обычно фиксируется при старте container, а mounted file может обновляться с задержкой; приложение должно уметь перечитывать его.

ConfigMap не шифрует данные и не предназначен для credentials. Versioned configuration полезнее mutable object без связи с rollout: изменение должно быть обнаруживаемым и проверяемым.

## Диагностика

Проверяйте namespace, key name, volume mount, Pod template, application startup log и checksum annotation. Не печатайте конфигурацию, если она может содержать credentials.

## Типичные ошибки

- secret в ConfigMap;
- ожидание обновления environment без restart;
- изменение ConfigMap без rollout policy;
- неправильный key/path;
- конфигурация не совместима с версией image.

## Практика

Передайте конфигурацию и как environment, и как file, измените object, сравните поведение приложения и добавьте version/checksum в Deployment.

## Следующие темы

`15-Secrets-and-Secret-Delivery.md`, probes и configuration promotion.
