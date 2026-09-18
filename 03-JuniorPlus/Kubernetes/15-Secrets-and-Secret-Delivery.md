# Secrets и secret delivery

## Цель

Понять lifecycle Kubernetes Secret и границы его защиты в API, etcd, Pod и logs.

## Prerequisites

Authentication/authorization, TLS, ConfigMaps, RBAC и secret lifecycle.

## In scope

Secret object хранит чувствительные values, но base64 не является шифрованием. Защита зависит от TLS to API Server, encryption at rest в etcd, RBAC, audit, namespace isolation и доступа process к mounted secret.

Secret может быть environment или volume. Rotation должна учитывать, видит ли приложение новое значение без restart, сколько живёт старое соединение и как отзывается старый credential. В production предпочтительнее external secret manager с controlled delivery.

## Диагностика

Проверяйте secret reference, RBAC, Pod events, mount permissions и rotation timestamp, но не выводите значение. Утечку считайте compromise: revoke/rotate и investigate logs/artifacts.

## Типичные ошибки

- хранить plaintext secret в Git;
- считать base64 encryption;
- дать namespace-wide read всем service accounts;
- забыть rotation и revoke;
- печатать secret в debug/log.

## Практика

Создайте Secret с ограниченным ServiceAccount, подключите его volume, выполните controlled rotation и подтвердите, что старое значение больше не принимается.

## Следующие темы

`16-ServiceAccounts-and-RBAC-Basics.md`, security и external secret delivery.
