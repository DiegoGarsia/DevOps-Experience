# Ресурсы VM и модель устройств

## Цель

Научиться связывать настройки VM с видимыми внутри guest устройствами и с operational последствиями.

## Prerequisites

KVM, CPU/memory virtualization и Linux devices.

## In scope

VM получает virtual BIOS/UEFI, controllers, disks, NIC, clock source, consoles и optional guest agents. Выбор device model влияет на совместимость и производительность: paravirtualized devices обычно эффективнее эмулированных legacy devices.

Guest agent может передавать host информацию о состоянии VM, IP и shutdown request, но не является обязательным условием работы ОС. Clock источник и suspend/snapshot влияют на time synchronization.

Ресурсная квота — это не всегда гарантированная производительность. Нужно различать limit, reservation, shares и burst. В production фиксируйте, какие guarantees реально предоставляет hypervisor.

## Диагностика

Сравнивайте конфигурацию VM с `lspci`, `lsblk`, `ip link`, guest agent status и kernel logs. Если device отсутствует, проблема может быть в VM definition, driver, udev или guest configuration.

## Типичные ошибки

- менять virtual hardware без compatibility plan;
- удалять disk device, не проверив serial/UUID;
- считать guest agent аналогом monitoring;
- не учитывать clock drift после suspend;
- давать лишние devices без security необходимости.

## Практика

Добавьте и удалите дополнительный virtual NIC и disk в test VM, определите их в guest по stable identifiers и сопоставьте с hypervisor configuration.

## Следующие темы

`04-Virtual-Disks-Storage-and-Snapshots.md` и `05-Virtual-Networks-Bridges-and-VLANs.md`.
