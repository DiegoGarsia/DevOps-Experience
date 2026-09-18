# File descriptors и I/O

## Цель

Понять, как процесс получает доступ к файлу, socket или pipe и почему проблемы с descriptor могут выглядеть как утечка ресурсов или зависание сервиса.

## Prerequisites

Файлы, filesystem, links и базовая модель системных вызовов.

## In scope

File descriptor — небольшое целое число, являющееся индексом в таблице открытых объектов процесса. Descriptor может ссылаться на обычный файл, pipe, socket, device или другой kernel object. По соглашению `0` — stdin, `1` — stdout, `2` — stderr.

Открытие файла создаёт kernel-level open file description с текущей позицией, режимом и ссылкой на inode. После `fork` дочерний процесс наследует descriptors; при этом два процесса могут совместно владеть одной позицией чтения. `dup` создаёт ещё один descriptor на то же open file description.

I/O бывает blocking и non-blocking. В blocking режиме системный вызов может ждать данные, место в буфере или завершение устройства. В network service это влияет на количество threads и event loop. Буферизация в user space, kernel page cache и буфер устройства — разные уровни.

```text
Процесс
  └── fd 3 ─── open file description ─── inode / socket / pipe
```

## Диагностика

```bash
ls -l /proc/$PID/fd
lsof -p "$PID"
cat /proc/$PID/limits
```

Если диск заполнен, но `du` не объясняет размер, ищите удалённые открытые файлы через `lsof`. Если сервис достиг лимита descriptors, проверяйте `ulimit`, системные limits и реальное количество connections/files.

## Типичные ошибки

- закрывать descriptor дважды или не закрывать его вообще;
- считать file descriptor равным inode;
- забывать, что socket тоже занимает descriptor;
- увеличивать `ulimit` вместо устранения утечки;
- анализировать I/O только по CPU usage.

## Практика

Запустите процесс, создающий файл и socket, найдите его descriptors через `/proc`, удалите открытый файл и объясните, почему место не освободилось сразу.

## Следующие темы

`08-Processes-PID-PPID-and-States.md`, `13-Memory-and-Virtual-Memory.md` и networking fundamentals.
