# Page cache, swap и OOM

## Цель

Различать cache, swap и memory pressure и понимать, почему OOM — это результат политики нехватки памяти, а не отдельная причина сбоя приложения.

## Prerequisites

`13-Memory-and-Virtual-Memory.md`.

## In scope

Page cache хранит недавно использованные данные файлов. Чистые страницы можно выбросить и прочитать заново, dirty pages сначала нужно записать на storage. Cache ускоряет повторный I/O, но конкурирует с anonymous memory за RAM.

Swap позволяет переместить часть anonymous pages на более медленное хранилище. Наличие swap не означает, что система постоянно «работает с диска»: важны swap-in/swap-out rates и latency. Агрессивный swap может увеличить задержки, но отключение swap не является универсальным лечением.

Когда доступной памяти недостаточно, ядро применяет reclaim, учитывает pressure и может запустить OOM killer. Выбор жертвы зависит от memory footprint, `oom_score_adj`, cgroup limits и других факторов. В контейнере OOM может произойти внутри cgroup, не исчерпав всю RAM host.

## Диагностика

```bash
free -h
swapon --show
vmstat 1
dmesg -T | grep -i -E 'oom|out of memory|killed process'
cat /proc/pressure/memory
```

Сначала отличайте общий OOM host от cgroup OOM. Затем фиксируйте, какой процесс был завершён, сколько памяти он использовал и почему лимит оказался недостаточным.

## Типичные ошибки

- очищать page cache как регулярную «оптимизацию»;
- считать наличие swap доказательством нехватки RAM;
- смотреть только на `free`;
- увеличивать memory limit без оценки leak, cache и рабочей нагрузки;
- не проверять kernel logs после внезапного завершения процесса.

## Практика

В безопасной VM создайте controlled memory pressure, наблюдайте `vmstat`, PSI и kernel log, затем сравните поведение host и ограниченного cgroup.

## Следующие темы

`15-Users-Groups-and-Identity.md`, `15-Resource-Limits-and-ulimits.md` уровня Junior и `03-cgroups-Resources-and-OOM.md`.
