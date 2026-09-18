# `/proc` и `/sys`: виртуальные интерфейсы ядра

## Цель

Научиться использовать `/proc` и `/sys` как источники наблюдений о процессах, ядре и устройствах, не путая их с обычной файловой системой.

## Prerequisites

Процессы, descriptors и базовая архитектура Linux.

## In scope

`/proc` предоставляет сведения о процессах и состоянии ядра во время выполнения. Каталоги `/proc/$PID` содержат cmdline, environment, status, maps, file descriptors и limits конкретного процесса. Системные файлы вроде `/proc/meminfo`, `/proc/loadavg`, `/proc/net` дают агрегированное состояние.

`/sys` — sysfs, иерархия объектов ядра, устройств, драйверов, block devices и параметров. Часть файлов доступна для записи, но запись изменяет runtime behavior и требует осторожности.

```text
/proc → процессы и runtime state
/sys  → устройства, drivers, kernel objects и параметры
```

Значения не обязаны быть стабильными между чтениями. Например, `/proc/stat` и `/proc/meminfo` описывают момент времени. Для rates нужно снять две точки и вычислить разницу.

## Диагностические примеры

```bash
cat /proc/loadavg
cat /proc/meminfo
tr '\0' ' ' < /proc/$PID/cmdline
tr '\0' '\n' < /proc/$PID/environ
readlink /proc/$PID/exe
ls -l /sys/class/block
```

`/proc/$PID/environ` может содержать секреты, поэтому его нельзя бездумно отправлять в ticket или лог.

## Ограничения

Эти интерфейсы Linux-specific, формат некоторых файлов не является стабильным API для приложений. Для постоянного мониторинга лучше использовать exporter или документированный интерфейс, а `/proc` и `/sys` применять для расследования и локальных проверок.

## Практика

Выберите процесс, зафиксируйте его командную строку, открытые descriptors, limits и memory maps. Сопоставьте эти данные с наблюдаемым поведением процесса.

## Следующие темы

`13-Memory-and-Virtual-Memory.md`, `15-Users-Groups-and-Identity.md` и Junior topics `sysctl`/resource limits.
