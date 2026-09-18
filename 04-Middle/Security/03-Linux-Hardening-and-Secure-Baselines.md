# Linux hardening и secure baselines

## Цель

Создать измеримый host baseline, который снижает attack surface и остаётся совместимым с operational access.

## Prerequisites

Linux services, permissions, SSH, firewall, package management, systemd и logs.

## In scope

Baseline включает patch policy, минимальные packages/services, SSH, users/sudo, filesystem permissions, firewall, time, audit, kernel parameters, resource limits и log retention. Каждая настройка должна иметь rationale, test и rollback.

## Практика

Примените baseline к test VM через Ansible, сравните открытые ports/users/services до и после и проверьте recovery access.

## Следующие темы

`04-Kubernetes-RBAC-Design.md`, supply chain и vulnerability management.
