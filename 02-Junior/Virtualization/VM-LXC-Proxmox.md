# Виртуализация: VM, LXC и Proxmox

## Модель

```text
физический сервер
  ↓
hypervisor KVM
  ↓
VM: виртуальные CPU/RAM/disk/NIC → guest kernel → процессы
```

VM виртуализирует hardware и запускает отдельный guest kernel. LXC — изоляция на уровне ОС: процессы используют kernel хоста, а namespaces/cgroups ограничивают видимость и ресурсы.

## Зачем это нужно

VM даёт изоляцию и возможность использовать разные kernels/ОС. LXC быстрее запускается и плотнее использует ресурсы. Выбор — компромисс между изоляцией, overhead, portability, контролем kernel и сложностью эксплуатации.

Virtual disk бывает image/block volume, virtual NIC подключается к bridge. Snapshot фиксирует состояние на storage layer. Template ускоряет создание одинаковых instances, а backup должен иметь независимую копию и проверку восстановления.

## Proxmox как реализация

Proxmox предоставляет management layer над KVM/LXC, storage, bridge, templates, snapshots и backup.

```bash
qm list
pct list
qm config VMID
pct config CTID
qm terminal VMID
pct enter CTID
qm snapshot VMID before-change
pct snapshot CTID before-change
```

## Отказы

- overcommit ресурсов → steal time, memory pressure, I/O wait;
- нет сети VM → guest interface/config, bridge/VLAN или host firewall;
- rollback не содержит последние данные → snapshot не является off-host backup;
- LXC требует недоступную kernel feature → выбрать VM или изменить дизайн;
- storage заполнен → затронуты многие guests, нужны capacity alerts и headroom.

## Вопросы

- **VM и LXC?** VM имеет отдельный kernel и более сильную границу, LXC использует kernel хоста и легче.
- **Snapshot и backup?** Snapshot — быстрый локальный rollback, backup — независимое восстановление.
- **Почему container не VM?** Container изолирует процессы kernel primitives, а VM виртуализирует hardware и guest kernel.

## Как объяснить за 30–60 секунд

«Виртуализация определяет, где находится граница изоляции. KVM VM виртуализирует hardware и имеет собственный kernel, LXC использует kernel хоста с namespaces/cgroups. Proxmox — управленческая реализация, а выбор VM/LXC зависит от kernel needs, isolation, density и recovery».

[[01-Fundamentals/Linux/Kernel-Processes-Memory]], [[01-Fundamentals/Linux/Filesystem-Storage]], [[02-Junior/Containers/Isolation-OCI-Runtime]].

## Ресурсы и наблюдаемость

VM получает virtual CPU, memory и disks; hypervisor планирует их на physical host. Overcommit позволяет выдать больше virtual resources, чем физически есть, но при pressure появляются steal time, swapping и I/O latency. Для LXC pressure действует через host kernel и cgroups.

Проверяй не только конфигурацию VM, но и фактический guest behavior: `uptime`, `free`, `iostat`, `ip route`, storage latency и host resource contention.

## Snapshot и backup

Snapshot — запись point-in-time состояния на том же storage domain. Crash-consistent snapshot не обязательно application-consistent: database могла иметь незаписанные buffers. Backup должен быть независимым, иметь retention, encryption/access policy и restore test.

## Практическое задание

1. Создай VM и LXC с одинаковым приложением, сравни boot time и resource usage.
2. Создай snapshot перед изменением сети, сломай config и выполни rollback.
3. Проверь, какие данные были созданы после snapshot и почему они исчезли.
4. Сделай отдельный backup и restore в новый instance.
5. Ограничь CPU/memory и наблюдай effect в guest.
