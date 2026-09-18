# Linux troubleshooting guide

## Процесс не работает

```text
systemctl/status или ps
↓
exit code/state/parent
↓
journal и application log
↓
permissions/environment/dependency/port
↓
исправление → повторный запуск → проверка
```

```bash
systemctl status APP
journalctl -u APP -b
ps -ef --forest
ss -ltnp
cat /proc/PID/status
```

## Высокий CPU/load

Сначала различи CPU saturation и I/O wait: `uptime`, `top`, `vmstat 1`, `iostat -xz 1`. Затем найди process/thread, сравни с deploy/query/logs и проверь, не создаёт ли service restart/fork storm.

## Память/OOM

`free -h`, `/proc/meminfo`, `vmstat`, process RSS, cgroup limits и kernel journal. Отличай host OOM от container OOMKilled. Workaround — ограничить нагрузку/rollback; permanent fix — leak/query/limit/capacity.

## Диск/filesystem

`df -h`, `df -i`, `du`, `lsof +L1`, `findmnt`, `lsblk`, `journalctl -k`. Read-only filesystem требует проверки storage/kernel errors, а не blind remount.

## SSH и service после reboot

Проверь DNS → TCP/port → `sshd -t` → `sshd -T` → key permissions → PAM/firewall. Для systemd сравни manual и unit environment, `User`, `WorkingDirectory`, `ExecStart`, dependencies и PATH.

## Root cause/prevention

Добавь resource alerts, service-level health, config validation, safe rollout, capacity headroom, backup и runbook. Инцидент закрывается только после повторного теста и фиксации prevention.

[[05-Production-Troubleshooting/Universal-Method]], [[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[01-Fundamentals/Linux/systemd-Users-SSH]].

## Полный сценарий: `df` 100%, `du` меньше

```text
symptom: application получает No space left on device
↓
df -h и df -ih
↓
findmnt TARGET
↓
du -xhd1 TARGET
↓
lsof +L1
↓
проверка inode/LVM/read-only/kernel errors
```

Если `df -ih` заполнен — ищи миллионы маленьких файлов. Если `lsof +L1` показывает большой deleted file — освободи место после controlled restart/close процесса. Если filesystem read-only — сохрани evidence и проверь storage/kernel, а не продолжай запись. Prevention: log rotation, disk/inode alerts, retention и capacity headroom.

## Полный сценарий: systemd failure

Проверь `systemctl status`, `journalctl -u -b`, `systemctl show`, затем `User`, `WorkingDirectory`, `ExecStart`, PATH, Environment, permissions, dependency и port. Сравни command вручную от того же user. После fix выполни `daemon-reload`, start, health check и наблюдение за restart loop.
