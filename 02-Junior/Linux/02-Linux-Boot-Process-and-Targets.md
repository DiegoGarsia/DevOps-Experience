# Boot process и targets Linux

## Цель

Понять путь от firmware до работающего service manager и локализовать сбой boot по этапу.

## Prerequisites

Linux architecture, processes, filesystems и package management.

## In scope

Упрощённая цепочка загрузки:

```text
Firmware/UEFI
  ↓ bootloader
Kernel + initramfs
  ↓
PID 1 / systemd
  ↓ target и units
Сервисы и login
```

Bootloader выбирает kernel и параметры. Initramfs подготавливает драйверы и root filesystem. После передачи управления kernel запускает PID 1. `systemd` строит dependency graph и активирует target, например multi-user или graphical.

Kernel parameters и initramfs влияют на root device, console, debugging и recovery. Rescue/emergency mode даёт минимальное окружение для исправления fstab, package или permission problems.

## Диагностика

```bash
systemctl get-default
systemctl list-jobs
journalctl -b -p err
systemd-analyze blame
systemd-analyze critical-chain
```

Указывайте номер boot через `journalctl -b -1`, если проблема проявилась после перезагрузки. Не принимайте долгий boot одного сервиса за общий kernel failure.

## Типичные ошибки

- менять boot parameters без доступного recovery;
- исправлять systemd unit, когда проблема в initramfs или root filesystem;
- считать target линейным списком команд;
- не сохранять старую рабочую kernel entry.

## Практика

На тестовой VM создайте медленный unit, исследуйте `critical-chain`, затем переведите систему в rescue target и восстановите безопасный boot.

## Следующие темы

`03-Disks-Partitions-and-Block-Devices.md`, `07-systemd-Units-and-Unit-Lifecycle.md`.
