# OCI images и image layout

## Цель

Понять, из чего состоит container image и как registry/runtime находят содержимое по digest.

## Prerequisites

Filesystem layers, structured metadata и container model.

## In scope

OCI image состоит из manifest, config и layers. Manifest связывает архитектуру/OS с config и ordered layer descriptors. Layer — tar-представление изменений filesystem, обычно с compression при передаче. Digest обеспечивает content addressability и проверку целостности.

Image не является запущенным процессом. Config может описывать entrypoint, command, environment, working directory и user, но runtime применяет эти значения с учётом overrides.

Теги изменяемы, digest — более стабильная ссылка на конкретный content. Для production promotion предпочтительнее продвигать immutable digest, а не повторно использовать `latest`.

```text
tag → manifest → config + ordered layers
                         ↓
                    root filesystem
```

## Типичные ошибки

- считать tag immutable;
- добавлять секрет в layer и потом удалять его в следующем layer;
- смешивать архитектуры без multi-platform manifest;
- не проверять digest и signature;
- делать image из непредсказуемого build context.

## Диагностика

Проверяйте manifest, config, layer sizes, architecture и digest. Если image не запускается, отделяйте ошибку pull/verification от runtime command или filesystem.

## Практика

Исследуйте OCI image разных архитектур, найдите layer с лишним файлом и сравните tag с digest после нового push.

## Следующие темы

`06-Container-Runtimes-and-runc.md`, Docker images и registry.
