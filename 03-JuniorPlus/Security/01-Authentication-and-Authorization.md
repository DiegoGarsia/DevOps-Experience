# Authentication и authorization

## Цель

Различать доказательство identity и проверку разрешения на действие.

## Prerequisites

Users/groups, permissions, TLS, HTTP и API model.

## In scope

Authentication отвечает «кто ты?»: password, SSH key, certificate, token или workload identity. Authorization отвечает «что этому субъекту разрешено?»: action, resource, scope и policy.

Identity lifecycle включает выдачу, rotation, revocation, expiration и audit. Без authorization сильная authentication не защищает ресурс; без authentication policy не знает, к кому применять правило.

## Диагностика

Разделяйте connection/TLS failure, invalid credential, expired token, `401` и `403`. Проверяйте issuer, audience, scope, clock sync и policy evaluation.

## Типичные ошибки

- исправлять `403` повторной выдачей password;
- долгоживущий token без rotation;
- доверять user-provided identity header;
- не проверять audience/issuer;
- общий account без audit.

## Практика

Настройте API с authentication и двумя roles, проверьте valid/expired credential и различие `401`/`403`.

## Следующие темы

`02-Identity-Least-Privilege-and-RBAC.md`, secrets и TLS.
