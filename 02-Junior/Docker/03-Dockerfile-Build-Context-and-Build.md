# Dockerfile, build context и build

## Цель

Понять, как Docker преобразует Dockerfile и context в image и как исключить случайную отправку лишних или секретных данных.

## Prerequisites

Images/layers, shell, filesystems и базовая сборка приложения.

## In scope

Build context — набор файлов, переданный builder. `.dockerignore` исключает ненужные данные, но не является security boundary, если секрет уже попал в другой слой или build argument.

Основные инструкции:

- `FROM` задаёт base image;
- `WORKDIR` фиксирует рабочий каталог;
- `COPY` переносит файлы из context;
- `RUN` выполняет build operation;
- `ENV` задаёт runtime/build environment;
- `USER` снижает runtime privilege;
- `ENTRYPOINT` и `CMD` задают запуск.

Build должен быть воспроизводимым: pin base image digest, lock dependencies, фиксируйте compiler/runtime version и не скачивайте непроверенные данные без контроля checksum.

## Диагностика

Проверяйте context size, build logs, cache hit/miss, user, working directory и итоговый command. Если файл «не найден», сначала выясните, входит ли он в context.

## Типичные ошибки

- отправлять `.git`, SSH keys и secrets в context;
- использовать `ADD` без понимания его поведения;
- запускать приложение от root;
- устанавливать latest dependency без lock;
- смешивать build-time и runtime configuration.

## Практика

Соберите небольшое приложение с `.dockerignore`, non-root user и pinned dependency. Проверьте, что секретный файл не попал в context и image layers.

## Следующие темы

`04-Build-Cache-Multi-Stage-and-Reproducibility.md`, container lifecycle и security.
