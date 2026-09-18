# `sysctl` и параметры ядра

## Цель

Понять, как runtime-параметры меняют поведение kernel и почему tuning без измерения может ухудшить систему.

## Prerequisites

Linux architecture, `/proc`, `/sys`, networking, memory и resource limits.

## In scope

`sysctl` читает и изменяет параметры kernel interface, многие из которых представлены в `/proc/sys`. Параметры могут влиять на forwarding, socket buffers, connection queues, VM reclaim и security hardening.

Изменение делится на runtime и persistent. Runtime применяется немедленно, но может исчезнуть после reboot. Persistent configuration обычно находится в `/etc/sysctl.d/*.conf` и применяется через `sysctl --system`.

```bash
sysctl net.ipv4.ip_forward
sysctl -a | grep '^vm\.'
sysctl -w net.ipv4.ip_forward=1
sysctl --system
```

Изменяйте один параметр за раз, фиксируйте старое значение, причину и expected signal. Например, увеличить socket buffer недостаточно, если bottleneck находится в application queue или NIC.

## Диагностика

Сравнивайте effective value, persistent files, kernel logs и метрику, которую хотите изменить. После reboot проверяйте, что параметр применился именно к нужному host и namespace.

## Типичные ошибки

- копировать tuning из другой системы;
- применять security parameter без проверки доступности;
- путать host sysctl и namespace-specific sysctl;
- менять десятки значений одновременно;
- не хранить изменение в configuration management.

## Практика

В test VM включите IP forwarding, проверьте routing behavior, сохраните настройку через отдельный sysctl drop-in и откатите её.

## Следующие темы

Virtualization, containers, cgroups и Docker networking.
