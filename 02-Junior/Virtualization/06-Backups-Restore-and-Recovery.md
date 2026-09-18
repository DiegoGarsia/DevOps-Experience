# Backup, restore и recovery виртуальной инфраструктуры

## Цель

Понять backup как проверяемый процесс восстановления, а не как факт наличия архива.

## Prerequisites

Virtual disks, snapshots, storage, сети и RPO/RTO на базовом уровне.

## In scope

Backup — независимая копия данных или конфигурации, которую можно восстановить в другом failure domain. Snapshot удобен для короткой операции, но зависит от исходного storage и может быть crash-consistent.

Для VM нужно определить:

- что копируется: disk, metadata, network definition, secrets;
- частоту и retention;
- допустимую потерю данных — RPO;
- допустимое время восстановления — RTO;
- шифрование и доступ;
- offsite или immutable copy;
- процедуру restore и verification.

Правило «backup successful» означает только успешное создание копии. Recovery test проверяет, что из неё можно поднять usable VM, подключить storage, восстановить identity и пройти application health check.

## Типы recovery

- restore поверх существующей VM — быстро, но риск перепутать состояния;
- restore в новую VM — безопаснее для проверки;
- bare-metal или platform recovery — нужен при отказе управляющей системы;
- partial restore — только нужные данные или disk.

## Типичные ошибки

- backup всех VM на один storage;
- отсутствие ключей шифрования в плане recovery;
- restore без проверки DNS/IP collision;
- незадокументированные credentials;
- никогда не выполнять test restore.

## Практика

Создайте backup test VM, удалите её, восстановите под новым именем, проверьте disk, network, SSH и application state, затем оформите RPO/RTO и найденные gaps.

## Следующие темы

`07-LXC-Containers-vs-VMs.md`, Proxmox storage и Reliability backup testing.
