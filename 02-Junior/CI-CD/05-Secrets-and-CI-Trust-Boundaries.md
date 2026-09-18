# Secrets и trust boundaries CI

## Цель

Понять, какие job и runner имеют доступ к секрету и как не превратить pipeline в канал утечки или supply-chain атаки.

## Prerequisites

Authentication/authorization, environment variables, runners и Git branches.

## In scope

Secret должен иметь владельца, scope, срок жизни, rotation и audit. Protected variables доступны только доверенным branches/tags/environments. Untrusted merge request не должен получать production credential или возможность влиять на privileged runner.

Trust boundary проходит между repository, runner, container image, dependency registry, deployment cluster и target environment. Script из branch может выполнить любую доступную ему команду, поэтому masked variable не заменяет isolation.

Безопаснее использовать short-lived token, workload identity или secret manager, чем долгоживущий static password. Secret не должен попадать в arguments, process list, Docker layer, artifact, cache и logs.

## Диагностика

Ищите secret exposure в job log, artifact, cache, image history, shell trace и runner filesystem. После утечки credential нужно revoke/rotate, а не только удалить строку из log.

## Типичные ошибки

- protected secret в job для любой ветки;
- privileged shared runner;
- `set -x` рядом с секретом;
- secret в build argument;
- reuse одного token во всех средах.

## Практика

Создайте pipeline с untrusted и protected branch, ограничьте доступ к production secret, намеренно проверьте попытку доступа и выполните rotation test token.

## Следующие темы

`06-Rules-Needs-and-Parallelism.md`, security supply chain и environments.
