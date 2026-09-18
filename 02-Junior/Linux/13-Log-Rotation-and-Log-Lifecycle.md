# Log rotation и lifecycle логов

## Цель

Понять, как ограничить рост логов, не потерять события и не оставить сервис писать в удалённый файл.

## Prerequisites

journald, file descriptors, filesystem и time synchronization.

## In scope

Log lifecycle включает генерацию, доставку, rotation, compression, retention, archival и deletion. `logrotate` может переименовать файл, создать новый, сжать старый и попросить процесс reopen. Если процесс продолжает писать в старый descriptor, место продолжит расходоваться в удалённом файле.

С journald lifecycle управляется через limits и vacuum, а не только через rename файлов. Централизованный collector должен иметь backpressure, retry policy и защиту от дублирования.

Rotation должна учитывать:

- максимальный размер и время;
- частоту и количество архивов;
- права нового файла;
- сигнал или reload процесса;
- сохранение timestamp;
- требования аудита и retention.

## Диагностика

```bash
logrotate -d /etc/logrotate.conf
logrotate -f /etc/logrotate.conf
lsof +L1
journalctl --disk-usage
```

`lsof +L1` помогает найти удалённые открытые файлы. После rotation проверяйте, куда реально пишет процесс, а не только наличие нового имени.

## Типичные ошибки

- rotation без reopen;
- слишком короткий retention;
- сжатие активного файла;
- отсутствие места для временного архивирования;
- отсутствие контроля потерь при отправке в централизованное хранилище.

## Практика

Настройте rotation test log, намеренно удержите старый descriptor, найдите его через `lsof`, выполните reopen и подтвердите освобождение места.

## Следующие темы

`14-Time-Synchronization-and-NTP.md`, metrics/logs observability и production log policy.
