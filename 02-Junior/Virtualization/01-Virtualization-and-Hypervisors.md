# Виртуализация и hypervisor

## Цель

Понять, какую границу создаёт виртуальная машина и чем hypervisor отличается от обычного процесса и контейнера.

## Prerequisites

CPU, memory, storage, networking и Linux process model.

## In scope

Virtual machine получает virtual CPU, memory, devices и firmware environment. Hypervisor сопоставляет guest resources с host resources и перехватывает privileged operations. Type 1 обычно работает близко к hardware, type 2 работает поверх host OS; современные Linux-сценарии с KVM используют kernel-assisted virtualization.

VM изолирует kernel: guest может иметь собственную ОС, init, drivers и firewall. Это сильнее граница, чем обычный process namespace, но требует дополнительной памяти и I/O path.

```text
Guest application
  ↓ guest system calls
Guest kernel
  ↓ virtual device
Hypervisor/KVM
  ↓ host resources
Host kernel and hardware
```

## Trade-offs

VM удобна для изоляции, разных ОС и независимого lifecycle. Контейнер быстрее запускается и плотнее размещается, но разделяет kernel host. VM snapshot и live migration помогают операциям, но не отменяют backup и consistency требований.

## Типичные ошибки

- считать VM полностью независимой от host;
- выделять vCPU больше capacity без оценки contention;
- использовать snapshot как единственный backup;
- забывать про virtual network и storage bottleneck.

## Практика

Создайте test VM, зафиксируйте её virtual CPU, memory, disk и NIC, затем сравните process isolation VM и контейнера.

## Следующие темы

`02-KVM-and-CPU-Memory-Virtualization.md`, virtual disks и virtual networks.
