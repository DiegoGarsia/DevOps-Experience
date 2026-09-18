# Планирование CPU и load average

## Цель

Понимать, что означает загрузка CPU и load average, и не делать ложный вывод о проблеме только по одному числу.

## Prerequisites

Процессы, threads и их состояния.

## In scope

Планировщик выбирает runnable thread и предоставляет ему квант CPU. На многопроцессорной системе одновременно могут выполняться несколько threads, но runnable queue всё равно может расти. Context switch имеет стоимость: нужно сохранить состояние одного thread и восстановить другое.

Load average показывает среднее количество задач, которые либо готовы выполняться, либо находятся в uninterruptible sleep. Поэтому высокий load может быть следствием CPU saturation, дискового I/O, сетевого хранилища или зависшего устройства.

Интерпретировать load нужно относительно числа логических CPU и вместе с другими метриками:

- CPU user/system/idle;
- iowait;
- steal time в VM;
- runnable tasks;
- latency диска;
- memory pressure;
- context switches.

Высокий CPU и высокий load — одна ситуация. Высокий load при низком CPU может указывать на I/O wait или `D`-state processes. Высокий steal time означает, что гипервизор не предоставил VM обещанный CPU.

## Диагностика

```bash
uptime
top
vmstat 1
mpstat -P ALL 1
pidstat -w 1
```

Снимайте несколько интервалов, а не одну точку. Однократный spike не равен устойчивой перегрузке.

## Типичные ошибки

- сравнивать load с числом ядер без понимания I/O;
- считать `iowait` свободным CPU без проверки latency;
- игнорировать steal time в виртуальной машине;
- лечить высокий load перезапуском процесса без поиска блокирующего ресурса.

## Практика

Создайте CPU-bound workload, затем нагрузку на диск. Сравните load average, CPU states и process states. Объясните, почему числа отличаются.

## Следующие темы

`11-Signals-and-Process-Control.md`, `13-Memory-and-Virtual-Memory.md` и `15-Users-Groups-and-Identity.md`.
