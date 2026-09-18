# SSH configuration, forwarding и `ProxyJump`

## Цель

Научиться описывать повторяемые SSH-подключения и безопасно работать с jump host, port forwarding и серверной конфигурацией.

## Prerequisites

`21-SSH-Architecture-Authentication-and-Keys.md` и базовый routing.

## In scope

Пользовательская конфигурация обычно находится в `~/.ssh/config`, серверная — в `sshd_config` и drop-in каталогах. Алиас `Host` позволяет закрепить hostname, user, key, port и proxy.

Пример:

```sshconfig
Host app
    HostName 10.0.0.20
    User deploy
    IdentityFile ~/.ssh/id_ed25519_app
    IdentitiesOnly yes

Host db
    HostName 10.0.1.30
    User admin
    ProxyJump app
```

`ProxyJump` создаёт канал через промежуточный host, но конечный SSH-сервер всё равно видит отдельное соединение. Это безопаснее и понятнее, чем вручную строить цепочку shell-команд.

Port forwarding меняет достижимость:

- local forwarding `-L` публикует удалённый endpoint на клиенте;
- remote forwarding `-R` публикует endpoint на серверной стороне;
- dynamic forwarding `-D` создаёт SOCKS proxy.

Forwarding может обойти сетевые границы, поэтому должен быть ограничен политикой и аудитом. На сервере важны `AllowUsers`, `AllowGroups`, `PermitRootLogin`, `PasswordAuthentication`, `AllowTcpForwarding` и timeout settings.

## Диагностика

```bash
ssh -vvv app
sshd -T
ssh -G app
```

`ssh -G` показывает эффективную клиентскую конфигурацию, а `sshd -T` — серверную после применения defaults и includes.

## Типичные ошибки

- неправильные права на `~/.ssh` и private key;
- конфигурация применяется не к тому `Host` pattern;
- jump host доступен, но конечный маршрут отсутствует;
- forwarding включён без необходимости;
- изменение `sshd_config` применено без проверки синтаксиса и запасной сессии.

## Практика

Постройте lab-схему client → jump host → private host через `ProxyJump`, затем создайте временный local forwarding к внутреннему HTTP-сервису и удалите его после проверки.

## Следующие темы

Networking fundamentals, Junior systemd и SSH troubleshooting в Production.
