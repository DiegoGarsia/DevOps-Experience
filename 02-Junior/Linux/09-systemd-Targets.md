# systemd targets

## Цель

Понять target как группировку и точку синхронизации units, а не как последовательный сценарий запуска.

## Prerequisites

Units, services и dependencies.

## In scope

Target — unit, который обычно собирает dependencies. `multi-user.target` описывает состояние обычной серверной системы, `graphical.target` добавляет графическую среду, `rescue.target` и `emergency.target` предназначены для восстановления. Target может иметь `Wants`/`Requires`, но реальные ordering constraints задаются отдельно.

`default.target` — ссылка на target, который активируется после boot. `isolate` останавливает units, не входящие в выбранный target, поэтому команда опасна в удалённой сессии.

```bash
systemctl get-default
systemctl list-dependencies multi-user.target
systemctl set-default multi-user.target
systemctl isolate rescue.target
```

Перед сменой default target проверяйте доступность recovery console и SSH. Для production изменение target должно иметь rollback plan.

## Типичные ошибки

- считать target линейным списком;
- применять `isolate` на удалённом сервере без out-of-band доступа;
- включать сервис, но забывать привязать его к target;
- диагностировать failed service через смену target вместо чтения journal.

## Практика

Создайте custom target, добавьте в него test service, активируйте его в VM и верните исходный default target после проверки.

## Следующие темы

`10-systemd-Timers.md`, `11-Cron-and-Scheduled-Execution.md` и boot troubleshooting.
