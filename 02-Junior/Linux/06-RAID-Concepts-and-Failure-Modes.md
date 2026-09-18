# RAID: концепции и failure modes

## Цель

Понять, какую проблему решает RAID, какие компромиссы он создаёт и почему RAID не заменяет backup.

## Prerequisites

Block devices, LVM, filesystem и storage latency.

## In scope

RAID распределяет данные и/или redundancy между дисками. RAID 0 повышает aggregate throughput, но не переживает отказ. RAID 1 зеркалирует данные и переживает отказ одного устройства в паре. RAID 5 использует parity и теряет ёмкость под redundancy; RAID 6 выдерживает два отказа; RAID 10 сочетает mirroring и striping.

Важны не только уровень, но и размер stripe, rebuild time, write hole, нагрузка, controller, горячие spare и failure domain. Rebuild создаёт дополнительный I/O и может ухудшить состояние оставшихся дисков.

RAID повышает availability storage, но не защищает от удаления файла, corruption, ransomware, ошибки оператора или отказа всего массива. Backup нужен отдельно.

## Диагностика

Проверяйте состояние массива, degraded devices, rebuild progress, latency и kernel logs. Не извлекайте диск по одному лишь SMART warning без понимания роли устройства и процедуры замены.

## Типичные ошибки

- считать RAID 0 резервированным;
- проектировать по raw capacity без usable capacity;
- забывать, что два диска в одном shelf — один failure domain;
- запускать интенсивный rebuild в пик нагрузки;
- не проверять restore из backup.

## Практика

Создайте test software RAID, отключите одно устройство, наблюдайте degraded state и rebuild, затем восстановите массив и сравните latency до и после.

## Следующие темы

systemd storage units, backups/restore и virtualization storage.
