# Virtual disks, storage и snapshots

## Цель

Понять разницу между virtual disk, storage backend и snapshot и не использовать snapshot как замену backup.

## Prerequisites

Block devices, filesystems, LVM и VM device model.

## In scope

Virtual disk может быть file-backed, block-backed, thin-provisioned или размещённым на distributed storage. Guest видит device, но latency зависит от host filesystem, cache mode, backend и contention.

Snapshot фиксирует состояние или цепочку block changes на определённый момент. Во время активной записи snapshot может влиять на write amplification и performance. Для application-consistent snapshot нужны coordination, quiesce или database-aware procedure.

Crash-consistent snapshot похож на внезапное отключение питания: filesystem journal может восстановиться, но приложение могло не завершить transaction.

## Диагностика

Сопоставляйте guest I/O latency с host storage latency, queue depth, thin pool usage и snapshot chain. Проверяйте, не закончились ли metadata или физическое место backend.

## Типичные ошибки

- держать длинную snapshot chain;
- считать snapshot независимым от исходного storage;
- делать snapshot во время database write без consistency plan;
- thin-provisioning сверх capacity;
- не тестировать восстановление.

## Практика

Создайте snapshot test VM, измените данные, откатите snapshot и сравните guest filesystem state. Затем удалите snapshot и проверьте storage usage.

## Следующие темы

`05-Virtual-Networks-Bridges-and-VLANs.md`, `06-Backups-Restore-and-Recovery.md`.
