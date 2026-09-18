# Управление пакетами и репозитории

## Цель

Понять, как пакетный менеджер устанавливает software, разрешает зависимости и поддерживает воспроизводимое состояние host.

## Prerequisites

Linux filesystem, users, permissions, network и базовый shell.

## In scope

Пакет — архив программы, metadata и список зависимостей. Repository содержит индексы и подписанные пакеты. Package manager строит dependency graph, выбирает версии, скачивает архивы, проверяет подпись и выполняет lifecycle scripts.

На Debian-подобных системах используются `apt`/`dpkg`, на RHEL-подобных — `dnf`/`rpm`. Команды различаются, но модель одинакова: обновить metadata, установить точную или допустимую версию, проверить состояние и удалить пакет без нарушения зависимостей.

```bash
apt-cache policy nginx
apt install nginx
dpkg -L nginx
dnf repoquery --installed
rpm -qf /usr/sbin/sshd
```

Repository mirror, proxy cache и package lock влияют на скорость и воспроизводимость. В production нужно контролировать источник, подписи, версии и время обновления, а не выполнять массовый upgrade без окна и rollback plan.

## Диагностика

Разделяйте ошибки DNS/network, подписи, отсутствующего пакета, dependency conflict и post-install script. Проверяйте package database, repository configuration, свободное место и lock другой операции.

## Типичные ошибки

- подключать непроверенный repository;
- смешивать версии distribution;
- удалять пакет, не изучив reverse dependencies;
- обновлять kernel и сервисы без reboot/rollback plan;
- считать cache пакетов источником истины.

## Практика

Создайте локальный package cache, установите пакет из него, зафиксируйте список зависимостей и намеренно отключите repository, чтобы диагностировать отказ отдельно от ошибки приложения.

## Следующие темы

`02-Linux-Boot-Process-and-Targets.md`, systemd и configuration management.
