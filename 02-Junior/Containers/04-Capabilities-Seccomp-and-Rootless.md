# Capabilities, seccomp и rootless

## Цель

Понять, как уменьшить privilege контейнера и почему `root` внутри namespace не должен автоматически получать host capabilities.

## Prerequisites

Namespaces, users/groups, permissions и system calls.

## In scope

Capabilities разделяют полномочия root на части: например, `CAP_NET_BIND_SERVICE`, `CAP_NET_ADMIN`, `CAP_SYS_ADMIN`. Без нужной capability процесс может получить `EPERM`, даже если его UID равен 0 внутри контейнера.

Seccomp фильтрует набор system calls. Профиль должен быть минимальным, но слишком агрессивный профиль ломает runtime или приложение. AppArmor/SELinux дополняют модель path/label policy.

Rootless containers используют user namespace и запускаются без host root. Это уменьшает последствия компрометации, но ограничивает privileged networking, device access и некоторые filesystem operations.

## Безопасная стратегия

1. запускать non-root user;
2. drop all capabilities и добавлять только необходимое;
3. не использовать privileged mode без documented reason;
4. ограничить devices и mounts;
5. применить seccomp/LSM policy;
6. проверить работу в минимальной среде.

## Диагностика

Различайте permission error приложения, capability denial и seccomp denial. Смотрите runtime events и kernel audit logs, а не добавляйте `--privileged` вслепую.

## Типичные ошибки

- дать `CAP_SYS_ADMIN` «на всякий случай»;
- монтировать host `/` внутрь контейнера;
- считать rootless полностью эквивалентным rootful;
- отключить seccomp после первой ошибки;
- хранить secret в image.

## Практика

Запустите сервис non-root, уберите capability, необходимую для выбранной операции, найдите точную причину отказа и добавьте только минимальное разрешение.

## Следующие темы

`05-OCI-Images-and-Image-Layout.md`, runtime и container security.
