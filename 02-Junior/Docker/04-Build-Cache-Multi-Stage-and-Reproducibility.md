# Build cache, multi-stage и воспроизводимость

## Цель

Научиться ускорять build без скрытой зависимости от старого cache и уменьшать production image через multi-stage pattern.

## Prerequisites

Dockerfile, layers, dependencies и artifact lifecycle.

## In scope

Cache reuse зависит от порядка инструкций и содержимого context. Стабильные шаги вроде установки lock-файла обычно размещают раньше часто меняющегося source code. Cache не должен использоваться как единственный источник критических артефактов.

Multi-stage build отделяет builder image от runtime image:

```dockerfile
FROM golang:1.23 AS build
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /out/app ./cmd/app

FROM gcr.io/distroless/static-debian12
COPY --from=build /out/app /app
ENTRYPOINT ["/app"]
```

Воспроизводимость требует pinned base digest, lock-файлов, контроля внешних downloads, стабильного timezone/locale и документированного build environment.

## Типичные ошибки

- cache ускоряет build, но скрывает изменение dependency;
- builder tools попадают в runtime image;
- timestamp или случайный порядок файлов меняет digest;
- cache import из недоверенного источника;
- отсутствие SBOM/provenance.

## Диагностика

Сравнивайте build logs, layer history, digest и зависимости при clean build. Отдельно проверяйте результат с отключённым cache.

## Практика

Соберите image с cache, измените только source, затем dependency; сравните пересобранные layers. Перенесите приложение в multi-stage runtime и сравните размер и поверхность атаки.

## Следующие темы

`05-Container-Lifecycle-Resources-and-Environment.md`, CI image pipeline и registry.
