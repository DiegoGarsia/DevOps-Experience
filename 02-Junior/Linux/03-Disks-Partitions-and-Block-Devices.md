# Диски, partitions и block devices

## Цель

Научиться отличать физический диск, partition, block device и filesystem и безопасно готовить storage к использованию.

## Prerequisites

Filesystems, inode, mounts и boot basics.

## In scope

Block device предоставляет доступ к блокам данных. Диск может содержать partition table GPT/MBR, partitions могут объединяться в LVM или RAID, а filesystem создаётся поверх block device или логического тома.

```text
disk
  ↓ partition table
partition
  ↓ optional RAID/LVM
logical block device
  ↓ mkfs
filesystem
  ↓ mount
directory tree
```

Проверяйте topology до destructive command:

```bash
lsblk -o NAME,TYPE,SIZE,FSTYPE,MOUNTPOINTS,UUID
blkid
fdisk -l
```

Устройство может быть занято mount, swap, RAID или LVM. Увеличение block device не увеличивает автоматически partition и filesystem. Изменение partition table требует backup, корректного устройства и понимания сектора начала.

## Диагностика

Сопоставляйте `/dev/disk/by-id`, UUID и фактический device. После изменения проверяйте kernel logs, доступный размер и filesystem consistency. Не ориентируйтесь только на имя `/dev/sdX`: оно может измениться после перезагрузки.

## Типичные ошибки

- выполнить `mkfs` на неверном device;
- перепутать disk и partition;
- использовать `/dev/sdX` в fstab вместо стабильного UUID;
- расширить disk, но не partition/filesystem;
- игнорировать backup перед изменением таблицы разделов.

## Практика

Подключите дополнительный virtual disk к VM, зафиксируйте topology, создайте partition и проверьте её через UUID, не изменяя root disk.

## Следующие темы

`04-Mounts-fstab-and-Mount-Propagation.md`, LVM и RAID.
