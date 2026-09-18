# Потоки и выполнение процесса

## Цель

Понять разницу между процессом и thread и увидеть, как concurrency влияет на CPU, память, блокировки и диагностику.

## Prerequisites

`08-Processes-PID-PPID-and-States.md`.

## In scope

Процесс владеет адресным пространством и ресурсами, а threads внутри процесса разделяют память, открытые файлы и большую часть состояния. У каждого thread есть собственный stack, registers и scheduling state.

```text
Process
  ├── address space
  ├── file descriptors
  ├── credentials
  └── threads
       ├── stack
       ├── registers
       └── scheduling state
```

Threads дешевле процессов при создании и обмене данными, но общая память создаёт race conditions. Mutex, condition variable и atomic operations защищают критические участки, однако неправильная блокировка приводит к deadlock, starvation или contention.

Многопоточность не гарантирует ускорение: CPU-bound задача ограничена числом доступных CPU, а I/O-bound задача может выиграть от перекрытия ожидания и вычислений. В контейнере thread всё равно планируется ядром host, а `cgroups` могут ограничивать суммарное потребление группы.

## Диагностика

```bash
ps -eLf -p "$PID"
top -H -p "$PID"
cat /proc/$PID/task/$TID/status
```

Ищите конкретный thread, который потребляет CPU или удерживает блокировку. Усреднённое значение процесса может скрывать один горячий thread.

## Типичные ошибки

- считать каждый thread отдельным процессом;
- полагаться на количество workers без оценки CPU и I/O;
- увеличивать concurrency при уже перегруженной базе;
- диагностировать deadlock только по загрузке CPU.

## Практика

Сравните CPU-bound и I/O-bound программу с одним и несколькими threads. Зафиксируйте CPU usage, latency и количество context switches, затем объясните результат.

## Следующие темы

`10-CPU-Scheduling-and-Load-Average.md`, `13-Memory-and-Virtual-Memory.md` и `14-Page-Cache-Swap-and-OOM.md`.
