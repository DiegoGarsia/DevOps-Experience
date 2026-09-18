# Docker images, layers, tags и digests

## Цель

Понять, как image собирается из layers и почему production deployment должен опираться на digest, а не на плавающий tag.

## Prerequisites

OCI image layout, Docker architecture и filesystem layers.

## In scope

Каждая команда Dockerfile обычно создаёт layer. Layers content-addressed и переиспользуются между images, что экономит storage и ускоряет pull. Image имеет read-only layers, а запущенный container получает writable layer.

Tag — изменяемое имя, например `app:latest`. Digest указывает на конкретный manifest/content. Promotion одного и того же digest между environments устраняет повторную сборку и снижает риск расхождения артефактов.

```bash
docker image ls
docker image inspect app:1.2
docker image inspect app@sha256:<digest>
docker history app:1.2
```

Удаление tag не обязательно удаляет shared layers. Garbage collection registry и local prune требуют понимания references.

## Типичные ошибки

- использовать `latest` в production;
- хранить secret в layer;
- считать уменьшение числа строк Dockerfile уменьшением image;
- не учитывать architecture/OS;
- удалять image, используемый running container.

## Диагностика

Проверяйте manifest, digest, history, layer sizes, architecture и source registry. При расхождении environments сравнивайте именно digest.

## Практика

Соберите image дважды, сравните layer cache и digest, переместите digest в test environment и измените tag registry без пересборки.

## Следующие темы

`03-Dockerfile-Build-Context-and-Build.md`, cache и registry.
