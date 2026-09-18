# Linux: файловая система, mount и storage

## Модель

```text
block device → таблица разделов → partition/LVM → filesystem → mount point → pathname
```

Filesystem хранит metadata: inode, owner, mode, timestamps и ссылки на blocks. Directory entry связывает имя с inode. File descriptor — числовая ссылка процесса на открытый объект.

## Зачем это нужно

Абстракция filesystem позволяет приложениям использовать одинаковые `open/read/write` для ext4, XFS и сетевого storage. Mount объединяет разные filesystem в единое дерево `/`.

## Слои storage

- MBR/GPT описывают разделы диска;
- LVM строится как PV → VG → LV;
- filesystem может перейти в `ro` после ошибок;
- `df -h` показывает blocks, `df -i` — inodes;
- `fsync` и journal помогают после сбоя, но не являются backup;
- `noexec`, `nodev`, `nosuid`, `noatime` меняют безопасность и производительность.

## Команды

```bash
lsblk -f
blkid
findmnt
mount | column -t
df -hT
df -ih
sudo du -xhd1 /var | sort -h
sudo lsof +L1
sudo pvs; sudo vgs; sudo lvs
sudo fdisk -l
cat /etc/fstab
sudo mount -a
```

Если `df` и `du` расходятся, ищи deleted-but-open file, другой mount, inode exhaustion или reserved blocks. `lsof +L1` показывает удалённые, но ещё открытые файлы.

## Изменение размера

```text
backup/maintenance window
↓
проверить device/partition/PV/VG/LV/filesystem
↓
расширить нижний слой
↓
расширить верхний слой
↓
df/findmnt/тест записи
```

Расширение обычно безопаснее уменьшения. Для ext4 при уменьшении сначала уменьшают filesystem offline, затем LV; обратный порядок может уничтожить данные.

## Симптомы и причины

- `No space left` при свободных гигабайтах: inode, quota, deleted-open file или недорасширенный filesystem;
- mount после reboot не поднялся: UUID, тип, options или синтаксис `fstab`;
- filesystem read-only: kernel/storage errors; сначала изучи journal;
- данные исчезли после удаления контейнера: они были в writable layer, а не в volume;
- LVM не видит новый диск: не выполнен resize partition или PV.

## Вопросы

- **inode и block?** Block хранит данные, inode — metadata и ссылки на blocks.
- **Snapshot и backup?** Snapshot — быстрый локальный rollback, backup — независимый restore artifact.
- **Почему открытый удалённый файл занимает место?** Inode и blocks живут до закрытия последнего file descriptor.

## Как объяснить за 30–60 секунд

«Я мыслю storage слоями: device, partition, LVM, filesystem, mount и file descriptor. При заполнении сравниваю `df` и `du`, проверяю inodes и удалённые открытые файлы, затем LVM и kernel errors. Изменение размера делаю снизу вверх для расширения и только после backup для уменьшения».

[[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[04-Middle/Reliability/Availability-Backup-Recovery]].

## Учебный разбор с нуля

### Файл, inode и descriptor

Имя `/var/log/app.log` — это не сам файл, а запись каталога, указывающая на inode. Inode хранит owner, permissions, timestamps, размер и ссылки на blocks. Когда процесс выполняет `open`, kernel создаёт open file description и возвращает процессу file descriptor, например `3`.

```bash
printf 'hello\n' > /tmp/example.log
stat /tmp/example.log
ls -li /tmp/example.log
exec 3>/tmp/example.log
ls -l /proc/$$/fd/3
```

Ожидаемый результат: `stat` и `ls -li` показывают inode и metadata, а `/proc/$$/fd/3` связывает descriptor shell с файлом. Если файл удалить, пока FD открыт, имя исчезнет, но blocks останутся до `close`.

### Почему `df`, `du` и `lsof` нужны вместе

```bash
df -h /var
df -ih /var
sudo du -xhd1 /var | sort -h
sudo lsof +L1
findmnt /var
```

- `df -h` читает free blocks всего filesystem;
- `df -ih` показывает свободные inodes;
- `du` обходит видимые directory entries;
- `lsof +L1` находит удалённые открытые files;
- `findmnt` показывает, не скрывает ли отдельный mount содержимое нижнего каталога.

Если `df` показывает 100%, а `du` заметно меньше, не удаляй случайные файлы. Сначала проверь deleted-open files, другой mount и inodes.

### Mount и `/etc/fstab`

Mount соединяет filesystem с каталогом. До mount каталог может содержать файлы, но после mount они скрываются содержимым подключённого filesystem. Для постоянного mount используют UUID, потому что имена `/dev/sdX` могут измениться.

```bash
lsblk -f
sudo blkid
findmnt /data
sudo mount /dev/mapper/lab--vg-data /data
sudo umount /data
sudo mount -a
```

`mount -a` проверяет entries `fstab`, кроме некоторых специальных случаев. Перед reboot проверяй его в maintenance shell: ошибка UUID или option может остановить boot или перевести систему в emergency mode.

### LVM и безопасное расширение

LVM разбивает storage на extents. PV предоставляет physical extents, VG объединяет их в pool, LV получает диапазон extents и затем форматируется filesystem.

```bash
sudo pvs
sudo vgs
sudo lvs -o lv_name,vg_name,lv_size,lv_attr
sudo lvextend -L +5G /dev/lab-vg/data-lv
sudo resize2fs /dev/lab-vg/data-lv
```

Перед `lvextend` проверь свободное место в VG. `resize2fs` относится к ext2/3/4; у другого filesystem нужна своя команда. Ключ `-r` у подходящего `lvextend` может изменить filesystem автоматически, но результат всё равно проверяется через `df -hT` и тест записи.

### Практическое задание

1. Создай loopback device и filesystem в отдельном временном каталоге.
2. Примонтируй его по UUID с безопасными options.
3. Заполни blocks и отдельно создай много маленьких files, чтобы сравнить blocks/inodes.
4. Открой большой файл, удали его и найди через `lsof +L1`.
5. Создай LVM в лабораторной VM, расширь LV и filesystem.
6. Намеренно внеси ошибку в `fstab`, проверь `mount -a`, исправь её без reboot.

### Production implications

- disk pressure может остановить logs, container runtime и PostgreSQL WAL;
- filesystem read-only ломает application даже при доступных blocks;
- неправильный reclaim policy может удалить PV после удаления PVC;
- snapshot, replication и backup решают разные задачи;
- restore нужно проверять на реальной filesystem, permissions, ownership и свободном месте.

### Дополнительные вопросы Middle-уровня

- Почему удаление большого файла не всегда сразу освобождает место?
- Что происходит при внезапном отключении питания до `fsync`?
- Почему расширение partition, PV, LV и filesystem — четыре разные операции?
- Чем `hostPath` опаснее managed persistent storage?
