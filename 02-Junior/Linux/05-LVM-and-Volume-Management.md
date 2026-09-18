# LVM и управление томами

## Цель

Понять слои LVM и безопасно увеличивать логический том с учётом ограничений filesystem.

## Prerequisites

Block devices, partitions, filesystems и mounts.

## In scope

LVM строится из physical volumes, volume groups и logical volumes:

```text
PV → VG → LV → filesystem → mount
```

PV может быть partition или block device. VG объединяет extents, а LV получает логический диапазон, который можно расширять или, с дополнительными рисками, уменьшать. LVM не является backup и не гарантирует отказоустойчивость без нижнего storage design.

Типовой безопасный процесс расширения:

1. увеличить underlying disk или добавить PV;
2. проверить topology и свободные extents;
3. расширить VG/LV;
4. увеличить filesystem подходящей командой;
5. проверить размер, mount и application behavior.

```bash
pvs
vgs
lvs -a -o +devices
lvextend -r -L +10G /dev/vg0/data
```

Опция `-r` пытается расширить filesystem автоматически, но всё равно требуется проверка типа filesystem и backup plan.

## Диагностика

Сопоставляйте `lsblk`, `pvs`, `vgs`, `lvs`, `findmnt` и `df`. Разница между свободным местом VG и filesystem означает разные уровни проблемы.

## Типичные ошибки

- выполнить `lvextend` на неверном LV;
- забыть расширить filesystem;
- уменьшать mounted filesystem без поддерживаемой процедуры;
- считать snapshot LVM независимым backup;
- исчерпать VG из-за неучтённых thin volumes.

## Практика

Создайте VG и LV на test disk, смонтируйте filesystem, расширьте его в два этапа и подтвердите размер на каждом слое.

## Следующие темы

`06-RAID-Concepts-and-Failure-Modes.md`, backups и virtualization storage.
