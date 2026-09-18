# Resource limits и `ulimits`

## Цель

Понимать ограничения процесса и отличать лимит ресурса от фактического потребления и от cgroup limit.

## Prerequisites

Processes, file descriptors, memory и users/groups.

## In scope

`ulimit` показывает resource limits shell/process: число открытых файлов, размер stack, число процессов, core dump и другие значения. Есть soft limit, который действует сейчас, и hard limit, до которого процесс может поднять soft limit при достаточных правах.

Лимиты наследуются при запуске. Поэтому настройка интерактивного shell не обязательно применится к `systemd`, cron, container или CI runner. Для service используйте явную конфигурацию manager, а для контейнера учитывайте cgroup limits.

```bash
ulimit -n
cat /proc/$PID/limits
systemctl show example.service | grep -i limit
```

Увеличение `nofile` может быть необходимо для proxy, но оно не исправит connection leak. Рост числа descriptors также увеличивает память и сложность event loop.

## Диагностика

Сравните configured limit, effective limit процесса, текущий count descriptors и характер нагрузки. Ошибка `EMFILE` означает, что конкретный процесс достиг лимита открытых файлов, а не обязательно что host исчерпал ресурсы.

## Типичные ошибки

- менять `/etc/security/limits.conf` и ожидать эффекта у systemd;
- повышать limit до огромного значения без capacity assessment;
- путать soft/hard;
- не проверять limits дочернего процесса;
- диагностировать `Too many open files` только через disk usage.

## Практика

Ограничьте test process до малого числа descriptors, вызовите controlled leak, зафиксируйте error и найдите разницу между configured и effective limit.

## Следующие темы

`16-sysctl-and-Kernel-Parameters.md`, cgroups и container resources.
