# LXC и VM: сравнение границ

## Цель

Понять различие между system container и виртуальной машиной и выбрать подходящую изоляцию для задачи.

## Prerequisites

Namespaces, cgroups, filesystem, VM model и Linux permissions.

## In scope

VM запускает guest kernel и виртуальные devices. LXC использует kernel host, namespaces, cgroups, capabilities и filesystem tree. LXC обычно быстрее и плотнее, но ошибка в host kernel или privileged configuration может иметь больший blast radius.

| Свойство | VM | LXC |
|---|---|---|
| Kernel | отдельный guest kernel | общий kernel host |
| Запуск | тяжелее | быстрее |
| ОС | другая ОС возможна | совместима с host kernel |
| Изоляция | сильнее boundary | зависит от configuration |
| Storage/network | virtual devices | namespace и host integration |
| Типичный use case | сильная изоляция и разные ОС | лёгкие Linux workloads |

Privileged container уменьшает security boundary. Для недоверенного кода нужна более сильная изоляция и отдельная threat model.

## Типичные ошибки

- считать LXC равным OCI application container;
- запускать privileged LXC без необходимости;
- переносить VM assumptions на shared-kernel container;
- не учитывать host kernel upgrades.

## Практика

Создайте VM и LXC с одинаковым сервисом, сравните startup, process tree, filesystem, network namespace и последствия остановки host.

## Следующие темы

`08-Proxmox-Architecture-and-Resource-Model.md`, namespaces и container security.
