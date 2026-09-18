# Artifacts, cache и dependencies

## Цель

Разделять результат pipeline, ускоряющий cache и внешние зависимости, чтобы не скрывать ошибки воспроизводимости.

## Prerequisites

Jobs/runners, Git, package management и image lifecycle.

## In scope

Artifact — результат job, который нужен следующей стадии или сохраняется для deployment/debug. Он должен иметь retention, provenance и понятную версию. Cache ускоряет повторные builds, но его можно удалить и не следует считать источником истины.

Dependencies — внешние пакеты, base images, plugins и tool versions. Lock-файлы, checksum и mirror policy делают build предсказуемым.

```text
source commit
  ↓ build
artifact → deploy/promotion
cache ────→ ускоряет повторный build, но не заменяет artifact
```

## Типичные ошибки

- использовать cache как единственный результат build;
- сохранять секреты в artifact;
- artifact без commit/digest metadata;
- dependency без lock или checksum;
- бесконечный retention.

## Диагностика

Повторите job с очищенным cache, сравните artifact checksum, commit и dependency lock. Исследуйте, откуда runner скачал пакет или image.

## Практика

Создайте pipeline, который публикует checksum и artifact, затем удалите cache и повторите build, проверив воспроизводимость.

## Следующие темы

`04-Variables-and-Pipeline-Configuration.md`, secrets и immutable promotion.
