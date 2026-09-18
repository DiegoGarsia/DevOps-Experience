# systemd timers

## Цель

Научиться запускать периодические задачи через `systemd` с журналом, dependency model и контролем пропущенных запусков.

## Prerequisites

Units, services, targets и time synchronization.

## In scope

Timer unit активирует service unit. `OnCalendar` задаёт календарное расписание, `OnBootSec` и `OnUnitActiveSec` — интервалы относительно boot или предыдущей активации. `Persistent=true` может запустить пропущенную задачу после downtime.

Timer не должен содержать основную команду в себе: сервис описывает действие, timer — расписание. Это упрощает ручной запуск и диагностику.

```ini
[Unit]
Description=Daily example job

[Timer]
OnCalendar=*-*-* 03:00:00
Persistent=true
RandomizedDelaySec=10m

[Install]
WantedBy=timers.target
```

```bash
systemctl enable --now example.timer
systemctl list-timers --all
journalctl -u example.service
```

Время и timezone должны быть определены явно. `RandomizedDelaySec` снижает одновременный запуск одинаковых задач на множестве host.

## Типичные ошибки

- создавать timer без соответствующего service;
- не проверять missed run после reboot;
- выполнять destructive job без lock/idempotency;
- не учитывать длительность предыдущего запуска;
- запускать backup одновременно на всех узлах.

## Практика

Создайте timer, запускающий идемпотентный job каждые пять минут, проверьте журнал, остановите VM и убедитесь в поведении `Persistent` после boot.

## Следующие темы

`11-Cron-and-Scheduled-Execution.md`, journald и backup operations.
