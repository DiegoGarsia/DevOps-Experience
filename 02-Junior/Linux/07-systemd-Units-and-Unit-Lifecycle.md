# systemd units и lifecycle

## Цель

Понять unit model `systemd` и уметь читать effective configuration вместо копирования случайного unit-файла.

## Prerequisites

Boot process, процессы, environment и filesystem.

## In scope

Unit — декларативное описание ресурса, которым управляет `systemd`: service, socket, mount, target, timer, path и другие типы. Unit имеет state и relationships. Unit-файл состоит из sections и options, а drop-in позволяет изменить отдельный параметр без копирования всего vendor file.

Жизненный цикл service обычно включает inactive, activating, active, deactivating и failed. `systemd` отслеживает main process, exit status, restart policy и readiness semantics.

```bash
systemctl status nginx
systemctl cat nginx
systemctl show nginx
systemctl list-unit-files
systemd-analyze verify /etc/systemd/system/example.service
```

`systemctl cat` показывает источники, а `systemctl show` — properties после применения defaults. После изменения unit нужен `daemon-reload`, но он только перечитывает конфигурацию, а не перезапускает сервис.

## Типичные ошибки

- редактировать vendor unit напрямую;
- забывать `daemon-reload`;
- путать enabled и active;
- проверять только файл, а не effective properties;
- менять `ExecStart` в drop-in без очистки предыдущего значения.

## Практика

Создайте собственный unit для простого процесса, добавьте drop-in с environment, проверьте unit через `systemd-analyze verify` и исследуйте все lifecycle states.

## Следующие темы

`08-systemd-Services-and-Dependencies.md`, `09-systemd-Targets.md` и journald.
