# Linux: ядро, процессы и память

## Что это

Ядро Linux — привилегированный слой, который управляет CPU, памятью, дисками, сетью, процессами и системными вызовами. Пользовательское пространство содержит программы и сервисы, обращающиеся к ядру через системные вызовы.

```text
приложение → библиотека/runtime → системный вызов → ядро → оборудование
```

Процесс — экземпляр программы с виртуальным адресным пространством, PID, родителем, учётными данными, открытыми дескрипторами и обработчиками сигналов. Потоки выполняются внутри процесса и разделяют его память.

## Зачем это нужно

Ядро изолирует адресные пространства, планирует CPU, проверяет права и предоставляет единый интерфейс к разным устройствам. Без него процессы могли бы читать память друг друга, напрямую управлять диском и блокировать CPU.

## Как это работает

- планировщик выбирает исполняемый поток;
- состояния процесса: `R` — готов к выполнению, `S` — ожидает, `D` — не прерываемое ожидание I/O, `T` — остановлен, `Z` — zombie;
- родитель забирает код завершения потомка через `wait`;
- `SIGTERM` просит процесс корректно завершиться, `SIGKILL` принудительно завершает его ядром;
- виртуальная память отображает виртуальные страницы в физические frames;
- page cache использует свободную RAM для файлов и может быть освобождён при давлении памяти;
- swap снижает вероятность немедленного OOM, но имеет большую задержку;
- OOM killer выбирает процесс, когда reclaim памяти не помогает.

## Команды и интерпретация

```bash
ps -eo pid,ppid,user,%cpu,%mem,stat,wchan:20,cmd --sort=-%cpu | head
pstree -ap PID
cat /proc/PID/status
cat /proc/PID/limits
ls -l /proc/PID/fd | wc -l
free -h
vmstat 1
cat /proc/meminfo
journalctl -k | grep -i -E 'oom|out of memory|killed process'
```

`ps` даёт снимок, `top`/`vmstat` — динамику. В `free` важнее `available`, а не только `used`. В `vmstat` высокий `si/so` означает активный swap, высокий `wa` — ожидание I/O. Load average включает runnable и uninterruptible tasks, поэтому высокий load бывает при проблемном диске даже при свободном CPU.

## Диагностика по симптому

| Симптом | Возможные причины | Следующая проверка |
|---|---|---|
| Высокий CPU | busy loop, плохой запрос, fork storm | `ps`, потоки, логи |
| Высокий load при низком CPU | disk/network I/O, процессы `D` | `vmstat`, `iostat`, `wchan` |
| OOMKilled | limit cgroup или нехватка памяти узла | limits, `memory.events`, kernel journal |
| Процесс не завершается | блокирующий I/O, parent leak, signal | state, `wchan`, open files |
| Zombie | родитель не вызвал `wait` | PPID, process tree, parent logs |

## Безопасность и производительность

Credentials, capabilities, namespaces, seccomp и cgroups ограничивают процесс. Не следует без измерений повышать limits или отключать OOM: это может превратить локальный сбой в отказ всего узла. Сначала раздели CPU saturation, memory pressure, I/O wait и ограничение cgroup.

## Вопросы

- **Процесс и поток?** Процесс имеет отдельное адресное пространство и ресурсы; потоки делят память процесса.
- **Почему cache не всегда проблема?** Page cache освобождается при необходимости.
- **Почему load не равен CPU?** В load входят runnable и uninterruptible задачи.
- **Что происходит после `fork`?** Создаётся потомок с copy-on-write страницами; физическая копия нужна только после записи.

## Как объяснить за 30–60 секунд

«Ядро управляет CPU, памятью, файлами и сетью через системные вызовы. Процесс имеет PID, адресное пространство, credentials и file descriptors; планировщик выбирает потоки, а виртуальная память использует page cache и swap. При проблеме я разделяю CPU, память и I/O с помощью `ps`, `vmstat`, `/proc` и kernel journal».

## Глубже

[[02-Junior/Containers/Isolation-OCI-Runtime]], [[05-Production-Troubleshooting/Linux]], cgroups v2, namespaces, capabilities, seccomp и perf.

## Учебный разбор с нуля

### Что происходит при запуске команды

1. Shell получает текст команды и разбирает его.
2. Shell ищет исполняемый файл по `PATH`.
3. Shell создаёт process, обычно через `fork`/`exec`.
4. Kernel создаёт PID, копирует environment и file descriptors.
5. Scheduler помещает поток в очередь runnable.
6. Процесс получает CPU time и делает system calls.
7. После завершения kernel записывает exit status.
8. Родитель читает status через `wait`; только после этого запись child исчезает из process table.

Если команда «зависла», это не означает, что CPU занят. Процесс может спать, ждать диск, socket, lock или дочерний процесс. Именно поэтому к `ps` нужно добавлять `stat`, `wchan`, open files и system logs.

### PID, PPID и сигналы на примере

```bash
sleep 300 &
echo $!
ps -o pid,ppid,stat,cmd -p PID
kill -TERM PID
```

Ожидаемый результат: после `kill -TERM` процесс завершается, а `ps` больше не показывает его. Если приложение имеет обработчик `SIGTERM`, оно может сначала закрыть соединения и записать сообщение в log. `SIGKILL` полезен как крайняя мера, но не даёт приложению cleanup.

### Почему виртуальная память важна

Процесс видит собственные virtual addresses, а kernel отображает их в physical pages. Поэтому:

- одинаковые shared libraries могут использовать общие физические страницы;
- `fork` сначала использует copy-on-write;
- page fault означает, что нужная page не находится в текущем mapping и kernel должен её загрузить/создать;
- RSS показывает resident pages, а VSZ — виртуальный диапазон, поэтому их нельзя путать;
- `free` показывает состояние host, а cgroup memory files — состояние ограниченного workload.

### Практическая последовательность при memory incident

```bash
free -h
vmstat 1 5
ps -eo pid,ppid,rss,vsz,%mem,stat,cmd --sort=-rss | head
cat /proc/PID/status | grep -E 'Vm|Threads'
cat /sys/fs/cgroup/memory.current 2>/dev/null
cat /sys/fs/cgroup/memory.events 2>/dev/null
journalctl -k -b | grep -i -E 'oom|killed'
```

Интерпретация:

- `available` низкий и растёт `si/so` → host memory pressure;
- один process имеет растущий RSS → вероятен leak или uncontrolled cache;
- `memory.events` содержит `oom` → cgroup ограничил workload;
- kernel journal содержит `Killed process` без cgroup evidence → искать host OOM;
- высокий VSZ при небольшом RSS может быть нормальным резервированием адресов.

### Load average на числовом примере

Узел с 4 CPU и load `8.0` не обязательно «в два раза перегружен»: нужно узнать, runnable ли задачи или они ждут I/O. Узел с 16 CPU и load `8.0` может иметь запас CPU. Сравнивай load с числом logical CPU, `%wa`, run queue и latency приложения.

```bash
nproc
uptime
top
vmstat 1
```

### Практическое задание

1. Запусти `sleep` в background и найди связь PID/PPID.
2. Создай процесс, который читает большой файл, и наблюдай `stat`/`wchan`.
3. Ограничь тестовый process cgroup memory limit.
4. Сравни host OOM и cgroup OOM в journal и `/sys/fs/cgroup`.
5. Объясни, почему process может быть `Running`, но application не отвечает.

### Production implications

- limits должны быть основаны на измерении, иначе появятся ложные OOM;
- слишком агрессивный OOM или restart loop создаёт cascade failure;
- открытые file descriptors и process count тоже являются ресурсами;
- scheduler starvation, lock contention и I/O wait видны как latency, а не только как CPU;
- metrics процесса, kernel journal и application logs должны связываться по PID/time/request id.

### Дополнительные вопросы Middle-уровня

- Чем user-space thread отличается от kernel scheduling entity?
- Почему `kill -9` может оставить данные в неконсистентном состоянии?
- Что происходит с child, если parent завершается?
- Почему page cache может улучшить производительность и одновременно скрыть реальное потребление памяти?
- Как отличить memory leak от нормального cache growth?
