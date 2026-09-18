# Синхронизация времени и NTP

## Цель

Понять, почему точное время необходимо для TLS, логов, distributed systems и корректного incident timeline.

## Prerequisites

Linux services, DNS, UDP/TCP и timezone basics.

## In scope

NTP синхронизирует часы host с time sources. Клиент измеряет offset и delay, выбирает источник и постепенно корректирует часы через slew либо делает step при большом расхождении. `chrony` и `systemd-timesyncd` — разные реализации, но задача одинакова.

Разделяйте system clock, hardware clock, timezone и формат отображения. UTC обычно используется для хранения и обмена, а локальная timezone — для представления пользователю.

Дрейф времени появляется из-за hardware, virtualization и нагрузки. В distributed systems clock нельзя считать идеальным: для порядка событий нужны monotonic clock, sequence или correlation ID.

## Диагностика

```bash
timedatectl status
chronyc sources -v
chronyc tracking
date -u
```

Проверяйте reachability NTP, stratum, offset, leap status и firewall. Несогласованные часы могут дать ложный вывод о порядке событий в логах.

## Типичные ошибки

- менять timezone вместо исправления времени;
- использовать один недоступный NTP source;
- сравнивать wall clock для измерения длительности;
- игнорировать time sync после snapshot VM;
- делать step в production без оценки приложений.

## Практика

Настройте test VM на два источника времени, отключите один, измерьте offset и сопоставьте timestamps между host и guest.

## Следующие темы

`15-Resource-Limits-and-ulimits.md`, journald и TLS certificate validation.
