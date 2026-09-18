# Mounts, `fstab` и mount propagation

## Цель

Понять, как block device или другое дерево становится доступным в namespace и как сделать mount воспроизводимым после reboot.

## Prerequisites

Block devices, filesystems, paths и namespaces на базовом уровне.

## In scope

Mount связывает filesystem tree с каталогом в текущем mount namespace. До mount каталог существует, но его содержимое скрывается содержимым mounted filesystem. `mount --bind` создаёт дополнительную точку доступа к существующему дереву.

`/etc/fstab` описывает источник, точку, тип, options, dump и fsck order. Надёжнее использовать UUID или стабильный by-id path. Options `nofail`, `x-systemd.automount` и timeout меняют boot behavior и должны применяться осознанно.

Mount namespaces позволяют разным процессам видеть разные деревья. Propagation `private`, `shared` и `slave` определяет, распространяется ли mount event между namespaces. Это становится важным для контейнеров и volume management.

```bash
findmnt
findmnt --verify
mount --bind /srv/data /mnt/data
umount /mnt/data
```

## Диагностика

Проверяйте `findmnt`, наличие source device, filesystem type, options, открытые файлы и зависимость mount от сети. Ошибка boot может быть следствием отсутствующего UUID или недоступного network filesystem.

## Типичные ошибки

- редактировать fstab без `findmnt --verify`;
- использовать каталог с данными до mount и потерять их под mounted tree;
- отмонтировать busy filesystem без поиска процесса;
- не учитывать propagation в container namespace;
- использовать `nofail` и скрыть критический отказ storage.

## Практика

Создайте filesystem на test volume, добавьте UUID в fstab, проверьте запись в отдельной VM и исследуйте mount propagation через bind mount.

## Следующие темы

`05-LVM-and-Volume-Management.md`, `06-RAID-Concepts-and-Failure-Modes.md`.
